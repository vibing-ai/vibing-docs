# Vibing AI Component Library (Block Kit)

Welcome to the Vibing AI Component Library (Block Kit) — a comprehensive, composable UI/UX toolkit for building rich, accessible, and consistent AI-powered applications, agents, and plugins on the Vibing AI platform.

---

## Overview

Vibing AI Block Kit provides a wide range of reusable, fully-typed React components and layout primitives, with built-in theming and accessibility. It is designed for rapid development of AI interfaces, supporting both light and dark modes, custom themes, and WCAG 2.1 AA accessibility.

---

## Block Kit Components

### Text Blocks
- **TextBlock**: Rich text content
- **MarkdownBlock**: Markdown with syntax highlighting
- **HeadingBlock**: Headings (various levels)

### Media Blocks
- **ImageBlock**: Images with captions/zoom
- **VideoBlock**: Embedded video
- **AudioBlock**: Embedded audio
- **FileBlock**: File attachments

### Interactive Blocks
- **InputBlock**: Text input with validation
- **FormBlock**: Multi-field forms
- **SliderBlock**: Range selection
- **CheckboxBlock**: Binary selection
- **RadioBlock**: Single selection
- **ButtonBlock**: Customizable buttons

### AI Blocks
- **AIChatBlock**: Conversational chat interface
- **AICompletionBlock**: Text completion with streaming
- **AIControlBlock**: AI parameter controls (temperature, model, etc.)

### Data Blocks
- **TableBlock**: Tabular data
- **ChartBlock**: Charts (various types)
- **DataGridBlock**: Advanced grid with editing
- **ListBlock**: Ordered/unordered lists
- **TreeBlock**: Hierarchical data

### Code Blocks
- **CodeBlock**: Syntax-highlighted code
- **CodeEditorBlock**: Editable code
- **TerminalBlock**: Terminal/console output

### Embed Blocks
- **IframeBlock**: External web content
- **TwitterBlock**: Twitter embeds
- **YouTubeBlock**: YouTube embeds
- **MapBlock**: Interactive maps

### Connector Blocks
- **FlowBlock**: Visual workflow connections
- **LinkBlock**: Hyperlinks
- **ReferenceBlock**: Reference by ID

---

## Layout & Surface Components

### Surfaces
- **Surface**: Base container
- **BlockContainer**: Consistent block styling
- **ContextPanel**: Side panel
- **Modal**: Dialog/modal
- **ToolPalette**: Floating toolbar
- **Widget**: Dashboard widget
- **ConversationCard**: Conversational UI card
- **NotificationSurface**: Notifications
- **CommandSurface**: Command palette
- **CanvasElement**: Draggable/resizable surface

### Composition & Views
- **DocumentView**: Linear document layout
- **BoardView**: Kanban board
- **GalleryView**: Grid gallery
- **MindMapView**: Mind map
- **TimelineView**: Timeline visualization

### Containers
- **BlockGroup**: Group related blocks
- **BlockStack**: Vertical/horizontal stack
- **BlockGrid**: Responsive grid
- **BlockSplitView**: Adjustable split view

### Low-level UI Components
- **Text**: Basic text
- **Button**: Button element
- **ScrollArea**: Custom scrollbars
- **Avatar**: User/entity avatar
- **Card**: Card container
- **Checkbox**: Checkbox
- **Radio**: Radio button
- **Select**: Dropdown
- **Tabs**: Tabbed interface
- **Tooltip**: Hover hints

---

## Theming

- **Theme Provider**: Use `BlockKitProvider` to set light, dark, or custom themes.
- **Custom Themes**: Use `createCustomTheme` for brand or product-specific styles.
- **Component Theming**: Override styles via the `style` prop.
- **HeroUI**: All components use HeroUI for consistent, accessible design tokens.

**Example:**
```jsx
<BlockKitProvider theme="dark">...</BlockKitProvider>
```

---

## Accessibility

Block Kit is built to WCAG 2.1 AA standards:
- **Keyboard Navigation**: Tab, Enter/Space, Escape, Arrow keys
- **Screen Reader Support**: ARIA roles/labels, live regions
- **Focus Management**: Trapping, restoration, visible indicators
- **Color Contrast**: 4.5:1 for text, 3:1 for UI controls
- **Modal Accessibility**: `aria-modal`, `aria-labelledby`, focus trapping
- **Utilities**: `getAccessibleColor` for color contrast
- **Testing**: Use `a11yCheck` from the SDK for automated accessibility tests

---

## Usage

- **Install**: `npm install @vibing-ai/block-kit`
- **Wrap your app**: `<BlockKitProvider theme="light|dark|custom">...</BlockKitProvider>`
- **Import components**: `import { TextBlock, AIChatBlock, ... } from '@vibing-ai/block-kit'`

---

For detailed API and usage examples, see the [Block Kit documentation](https://docs.vibing.im/block-kit). 