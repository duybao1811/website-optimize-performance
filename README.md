# website-optimize-performance

# 🚀 Optimizing Website Performance: From Lighthouse Score 33 to 99

Over the past weeks, I focused on improving the performance of a website project, and successfully raised its Lighthouse Performance score from **33 to 99**. This document summarizes the optimization techniques I applied, with practical explanations and code examples.

---

## 📌 Key Metrics to Understand First

Before diving into techniques, it's essential to understand these metrics that Lighthouse and other tools measure:

- **FCP (First Contentful Paint):** Time to render first text/image.
- **LCP (Largest Contentful Paint):** Time to render the largest element visible in viewport.
- **CLS (Cumulative Layout Shift):** Measures layout stability; high CLS indicates elements shifting while loading.

Optimizing these metrics directly impacts perceived performance and user experience.

---

## 1. 🎨 Font Optimization

### Problems with Google Fonts link:
- Using `<link href="https://fonts.googleapis.com/...">` causes extra DNS lookups and blocks rendering.

### Optimization Steps:
- Download fonts locally and load them using `@font-face`:

```css
@font-face {
  font-family: 'Inter';
  src: url('/fonts/Inter.woff2') format('woff2');
  font-display: swap;
}
```

### ✅ Preload Critical Assets
```html
<link rel="preload" href="/fonts/Inter.woff2" as="font" type="font/woff2" crossorigin>
```

### Benefits:
- Eliminates external requests
- Uses `font-display: swap` to prevent invisible text
- `.woff2` is compressed and loads faster
- When deployed on a CDN, self-hosted fonts load even faster

---

## 2. 🖼️ Image Optimization & Lazy Loading

- Use `loading="lazy"` for `<img>` tags
- Converted .jpg and .png images to .webp, reducing image sizes by 30-70% while maintaining good quality. .webp is more efficient than .jpg and .png in terms of compression:
    - JPG images: Can be reduced by 20-30% compared to .jpg while maintaining similar or better quality.
    - PNG images: Especially for images with transparency, .webp can reduce size by 40-80% compared to .png.
- Compress large images with [Squoosh](https://squoosh.app/)
- Lazy load components not visible initially (e.g., modal, tabs)

**Note:** For components rendered immediately, use fallback UIs (e.g., skeleton loaders) to avoid CLS.

### Preloading Heavy Images

To speed up the loading of important visual elements like **banners** or **background images** in the header, you can use **preloading**. This allows these assets to be fetched earlier in the page load process, improving perceived performance.

You can preload an image using the following code:

```html
<link rel="preload" href="path/to/your-image.webp" as="image" type="image/webp" />
```

---

### 3. 🧹 Clean Up Code

Cleaning up the code is an essential part of optimizing your website. It reduces the bundle size, improves maintainability, and enhances performance. Here’s how to clean up and optimize your code:

#### 3.1 Remove Unused Code

Eliminate any unused or redundant code (functions, variables, dependencies) to reduce bloat and improve performance. This can include removing unused libraries or refactoring old logic.

#### 3.2 Optimize Loops and Event Handlers

Ensure your loops and event handlers are optimized to prevent unnecessary recalculations and reflows in the browser. Avoid triggering **reflow** (recalculating layout) and **repaint** (redrawing elements) too many times, as these can significantly slow down your page. Batch DOM updates and use **`requestAnimationFrame`** for smoother animations.

#### 3.3 Defer or Lazy Load Non-Essential Code

Load only the critical code upfront and defer or lazy-load non-essential resources. This can include splitting code and loading images, components, or modules only when needed.

#### 3.4 Minify and Compress Your Code

Minify your JavaScript and CSS files to reduce file size and improve load times. Use tools like **Terser** for JS and **CSSNano** for CSS.

#### 3.5 Eliminate Console Logs in Production

Remove `console.log` statements from your production code, as they can impact performance, especially if called frequently inside loops or event handlers.

By cleaning up the code, you can ensure that your website loads faster, performs better, and remains easier to maintain.

---

## 4. 📦 Bundle Analysis & Optimization

### What is a Bundle?
Your final JavaScript bundle is what users download. Reducing its size improves FCP/LCP and TTI.

### Tools:
```bash
npx source-map-explorer build/static/js/*.js
```
This command will generate an interactive treemap of your JavaScript files, showing the relative size of each part of your bundle. This can help you quickly identify which libraries are taking up the most space.

### Changes I made:
- Replaced `moment.js` (~60KB) with `dayjs`
- Replaced `react-datepicker` (~265kb) with lighter `react-day-picker` (~66kb)
- Upgraded Firebase from v8 to v11 which supports **tree-shaking**

### 🔍 What is Tree-Shaking?
Tree-shaking removes unused code from libraries during bundling. You only ship the functions you use.

---

## 5. 📈 Tracking Scripts Optimization

- Delay analytics scripts like Google Analytics using `useEffect` so they run after page load:

```js
useEffect(() => {
  const script = document.createElement('script');
  script.src = 'https://www.googletagmanager.com/gtag/js?id=UA-XXXX';
  script.async = true;
  document.body.appendChild(script);
}, []);
```

---

## 6. 🪄 Virtual Scrolling

Rendering large lists? Use virtual scroll to only render visible items in viewport.

### Example Libraries:
- [@tanstack/virtual](https://tanstack.com/virtual)
- `react-virtualized`

This reduces DOM nodes, improves scroll performance and rendering speed.

---

## 7. ✂️ Critical CSS

**Critical CSS** is the technique of extracting and inlining the CSS that is required to render the above-the-fold content of a webpage, while deferring the loading of the rest of the styles. This ensures that the page is visually ready to interact with as quickly as possible.

#### Why is Critical CSS Important?

- **Faster First Paint:** By inlining critical styles, the browser can render the above-the-fold content without waiting for external CSS files to be fully loaded. This helps to reduce the **First Paint (FP)** and **First Contentful Paint (FCP)** times.
- **Avoiding Render-Blocking:** If CSS is not critical to the first render, it can be loaded asynchronously to improve page speed, reducing blocking times.
- **Improved User Experience:** A faster initial render reduces the time users have to wait, providing a smoother and more responsive experience.

#### How to Implement Critical CSS

1. Identify the critical CSS for the above-the-fold content.
2. Extract the critical CSS and inline it directly into the HTML `<head>` of the page.
3. Defer the non-critical CSS by loading it asynchronously or after the page has been rendered.

Example of inlining critical CSS:

```html
<head>
  <style>
    /* Critical CSS goes here */
    body {
      font-family: Arial, sans-serif;
      background-color: #fff;
    }
    h1 {
      font-size: 24px;
    }
  </style>
  <link rel="stylesheet" href="styles.css" media="non-crucial" />
</head>
```

- Use tools like [Critters](https://www.npmjs.com/package/critters) in your build setup
- Helps solve `Eliminate render-blocking resources` issue in Lighthouse

---

## 8. 🔧 JS Logic & Clean Code Optimization

- Clean and efficient logic = faster execution time
- Avoid unnecessary computations or large objects in memory
- Use hooks and dynamic imports wisely with libraries like Firebase to avoid early execution

---

## 9. ⚙️ Bonus Techniques

### ✅ Debounce/Throttle Input Events
```js
const handleResize = debounce(() => console.log('resized'), 200);
```

### ✅ Use Web Workers
Offload heavy tasks (e.g., parsing JSON, image processing) to a separate thread:
```js
const worker = new Worker('/worker.js');
worker.postMessage('start');
worker.onmessage = (e) => console.log(e.data);
```

---

## ✅ Result:
After applying these techniques, the Lighthouse Performance score increased from **33 to 99**, improving both user experience and SEO.

---

Thanks for reading! Feedback and suggestions are welcome 🙌

> ⭐ GitHub repo: [github.com/duybao1811/website-optimize-performance](https://github.com/duybao1811/website-optimize-performance)
