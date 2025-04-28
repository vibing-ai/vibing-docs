# Frequently Asked Questions (FAQ)

A collection of common questions and answers about using Vibing AI and the Block Kit component library.

---

**Q: My app's theme isn't applying correctly. What should I check?**
A: Ensure your application is wrapped in `<BlockKitProvider theme="light|dark|custom">`. Check for CSS conflicts with other libraries and verify that you are not using multiple theme providers.

**Q: Why isn't my component rendering or updating as expected?**
A: Make sure each block has a unique `id` prop and that all required props are provided. Inspect the browser console for error messages and check for missing or incorrect imports.

**Q: I'm getting API errors or my API calls aren't working. What should I do?**
A: Verify your API keys and authentication. Check network requests for errors in the browser's network tab. Ensure your request payloads match the expected format and handle errors gracefully in your code.

**Q: My app is slow or laggy. How can I improve performance?**
A: Avoid unnecessary re-renders by memoizing callbacks and using React's `useMemo`/`useCallback`. Use virtualization for long lists (e.g., `TimelineView` with `virtualizeItems`). Reduce the number of heavy components on a single page.

**Q: I'm seeing TypeScript errors with Block Kit components. How do I fix them?**
A: Ensure you are using the correct prop types and have the latest version of the library. Check for missing or incorrect imports and resolve any type conflicts with other libraries.

**Q: How do I ensure my app is accessible?**
A: All Block Kit components are built to WCAG 2.1 AA standards. Use semantic HTML, provide ARIA labels where needed, and test with screen readers. Use the `a11yCheck` utility from the SDK for automated accessibility tests.

**Q: How do I debug issues in my Block Kit app?**
A: Enable debug mode by setting `debug={true}` on `BlockKitProvider`. This will highlight component renders, log prop changes, and provide performance measurements.

**Q: Where can I get help or report bugs?**
A: You can:
- Email support: support@developers.vibing.im
- Join the community Discord: https://discord.gg/vibingai
- Open an issue on GitHub: https://github.com/vibing-ai/vibing-js (SDK) or https://github.com/vibing-ai/vibing-block-kit/issues (Block Kit)
- Ask on Stack Overflow: https://stackoverflow.com/questions/tagged/vibing-block-kit
- Attend office hours: Tuesdays 10am-12pm PT

---

For more troubleshooting tips, see the Troubleshooting Guide or the official documentation at https://docs.vibing.im/block-kit. 