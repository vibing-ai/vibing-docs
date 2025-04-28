# Troubleshooting Guide

This guide provides solutions to common issues encountered when building with Vibing AI and the Block Kit component library. If you run into problems, check the sections below for quick resolutions.

---

## Theming Issues
- **Symptoms:** Theme not applying, colors look wrong, dark/light mode not working.
- **Solutions:**
  - Ensure your app is wrapped in `<BlockKitProvider theme="light|dark|custom">`.
  - Check for CSS conflicts with other libraries.
  - Avoid using multiple theme providers in the same app.
  - Verify that your custom theme is correctly passed to `BlockKitProvider`.

## Component Rendering Problems
- **Symptoms:** Components do not appear, UI does not update, props seem ignored.
- **Solutions:**
  - Assign a unique `id` prop to every block/component.
  - Ensure all required props are provided.
  - Check the browser console for error messages.
  - Confirm that you are importing components from `@vibing-ai/block-kit`.
  - Make sure you are not using outdated or incompatible versions of the library.

## API Integration Issues
- **Symptoms:** API calls fail, data not loading, authentication errors.
- **Solutions:**
  - Verify your API keys and authentication tokens.
  - Inspect network requests in the browser's developer tools for errors.
  - Ensure your request payloads match the expected format.
  - Implement error handling for failed requests.
  - Check for rate limiting headers (`X-RateLimit-*`, `Retry-After`).

## Performance Problems
- **Symptoms:** Slow UI, laggy interactions, high memory usage.
- **Solutions:**
  - Avoid unnecessary re-renders by memoizing callbacks (`useCallback`) and values (`useMemo`).
  - Use virtualization for long lists (e.g., `TimelineView` with `virtualizeItems`).
  - Limit the number of heavy components on a single page.
  - Lazy-load complex or rarely used components with `React.lazy` and `Suspense`.

## TypeScript Errors
- **Symptoms:** Type errors, build failures, incorrect prop types.
- **Solutions:**
  - Use the latest version of Block Kit and its type definitions.
  - Check that you are using the correct prop types for each component.
  - Resolve any type conflicts with other libraries.
  - Ensure all required types are imported from `@vibing-ai/block-kit`.

## Accessibility Warnings
- **Symptoms:** Failing accessibility tests, missing ARIA labels, keyboard navigation issues.
- **Solutions:**
  - Use semantic HTML and provide ARIA labels where needed.
  - Test your app with screen readers and keyboard navigation.
  - Use the `a11yCheck` utility from the SDK for automated accessibility tests.
  - Ensure color contrast meets WCAG 2.1 AA standards (4.5:1 for text).
  - Use focus management utilities and visible focus indicators.

## Debugging Tools
- **Enable Debug Mode:**
  - Set `debug={true}` on `BlockKitProvider` to highlight component renders, log prop changes, and measure performance.
- **Check Console Logs:**
  - Review browser console for warnings and errors.
- **Testing Utilities:**
  - Use `BlockKitTestRenderer` and mock providers for unit and integration tests.
- **Visual Regression:**
  - Use Storybook, Chromatic, or Percy for visual testing.

---

If your issue is not resolved here, see the [FAQ](faq.md) or contact support (see Support & Contact below). 