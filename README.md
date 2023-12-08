## Migrating from Mantine v6 to v7 in Next.js:

Mantine v7 uses CSS modules by default, it means that you need to import styles from `@mantine/core/styles.css` to your project root file. The same is true for other packages, for example if you use `@mantine/notifications` you need to import `@mantine/notifications/styles.css` to your project root file.

#### Note: Mantine components cannot be used as server components. This guide will use the `pages` directory and assumes you have tailwind installed and configured.

### 1. Install dependencies

```sh
pnpm add @mantine/core @mantine/hooks @mantine/dates dayjs @mantine/notifications @mantine/dropzone @mantine/modals
```

### 2. Install postcss mantine presets.

```sh
pnpm add -D postcss-preset-mantine postcss-simple-vars
```

**Note: This is not required but highly recommended.**

### 3. Configure PostCSS

Inside your `postcss.config.js` file, add the following:

```js
module.exports = {
  plugins: {
    tailwindcss: {},
    autoprefixer: {},
    'postcss-preset-mantine': {},
    'postcss-simple-vars': {
      variables: {
        'mantine-breakpoint-xs': '36em',
        'mantine-breakpoint-sm': '48em',
        'mantine-breakpoint-md': '62em',
        'mantine-breakpoint-lg': '75em',
        'mantine-breakpoint-xl': '88em',
      },
    },
  },
};
```

### 4. Configure Next.js

Inside your `_app.tsx` file, add the following:

```tsx
import '@/styles/globals.css';
import '@mantine/core/styles.css';

// if you use notifications
import '@mantine/notifications/styles.css';
import type { AppProps } from 'next/app';
import { MantineProvider, createTheme } from '@mantine/core';
import { Notifications } from '@mantine/notifications';

const theme = createTheme({
  /** Put your theme overrides here */
});

export default function App({ Component, pageProps }: AppProps) {
  return (
    <MantineProvider theme={theme}>
      <Notifications />
      <Component {...pageProps} />
    </MantineProvider>
  );
}
```

Next, inside your `_document.tsx` file, add the following:

```tsx
import Document, { Html, Head, Main, NextScript } from 'next/document';

// note: this is required even if you use only one color scheme in your app
import { ColorSchemeScript } from '@mantine/core';

export default function Document() {
  return (
    <Html lang='en'>
      <Head>
        <ColorSchemeScript defaultColorScheme='auto' />
      </Head>
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  );
}
```

At this point you should be able to use Mantine components in your Next.js app like so:

### Using Button component

```tsx
import { Button } from '@mantine/core';

export default function Demo() {
  return <Button>Click me</Button>;
}
```

## Key differences between v6 and v7

- `createStyles` is no longer available, use [CSS Modules](https://mantine.dev/styles/css-modules/) instead
- `sx` prop is no longer available, use `className` or the `style` prop instead.
- Nested selectors are not supported in `style` and `styles` prop, use `classNames` instead

You can checkout the available CSS variables [here](https://mantine.dev/styles/css-variables/).

## Updating colors

You can add extra colors like so:

```tsx
// _app.tsx
import { createTheme } from '@mantine/core';

const theme = createTheme({
  colors: {
    'bright-pink': [
      '#F0BBDD',
      '#ED9BCF',
      '#EC7CC3',
      '#ED5DB8',
      '#F13EAF',
      '#F71FA7',
      '#FF00A1',
      '#E00890',
      '#C50E82',
      '#AD1374',
    ],
  },
});

export default function App({ Component, pageProps }: AppProps) {
  return (
    <MantineProvider theme={theme}>
      <Component {...pageProps} />
    </MantineProvider>
  );
}
```

Using the new color:

```tsx
import { Button } from '@mantine/core';

export default function Demo() {
  return <Button color='bright-pink'>Click me</Button>;
}
```
