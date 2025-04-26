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

## 3. 🧹 Clean Up Code

- Remove unused variables, components, and styles
- Reduce dead code that bloats the JS bundle

---

## 4. 📦 Bundle Analysis & Optimization

### What is a Bundle?
Your final JavaScript bundle is what users download. Reducing its size improves FCP/LCP and TTI.

### Tools:
```bash
npx source-map-explorer build/static/js/*.js
```

### Changes I made:
- Replaced `moment.js` (~60KB) with `dayjs`
- Replaced `react-datepicker` with lighter `react-daypicker`
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

Load only the CSS needed for above-the-fold content to reduce render-blocking and improve FCP.

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
