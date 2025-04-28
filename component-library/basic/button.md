---
title: 'Button'
description: 'Interactive button component for user actions'
---

# Button Component

The Button component provides a consistent way to trigger actions or navigate through the interface. It supports various styles, sizes, and states to accommodate different design needs.

## Import

```jsx
import { Button } from '@vibing-ai/block-kit';
```

## Basic Usage

```jsx
<Button>Click Me</Button>
```

## Variants

The Button component comes in several variants:

### Primary Button

Used for main actions and most important call-to-action elements.

```jsx
<Button variant="primary">Primary Action</Button>
```

### Secondary Button

Used for secondary actions that are still important but not the main focus.

```jsx
<Button variant="secondary">Secondary Action</Button>
```

### Tertiary Button

Used for less important actions, often as alternatives or cancelation options.

```jsx
<Button variant="tertiary">Tertiary Action</Button>
```

### Ghost Button

A minimal button style that appears as plain text until hovered or focused.

```jsx
<Button variant="ghost">Ghost Button</Button>
```

### Link Button

Styled like a link but behaves like a button with all button accessibility features.

```jsx
<Button variant="link">Link Button</Button>
```

### Danger Button

Indicates destructive actions like delete or remove.

```jsx
<Button variant="danger">Delete Item</Button>
```

## Sizes

Buttons come in multiple sizes to fit different contexts:

```jsx
<Button size="small">Small Button</Button>
<Button size="medium">Medium Button</Button> // Default
<Button size="large">Large Button</Button>
```

## States

Buttons can appear in different states:

### Disabled

```jsx
<Button disabled>Disabled Button</Button>
```

### Loading

Shows a spinner indicator while an action is processing.

```jsx
<Button loading>Loading Button</Button>
```

### Active

Indicates the button is currently active or selected.

```jsx
<Button active>Active Button</Button>
```

## Icons

Buttons can include icons before or after the text:

```jsx
<Button startIcon={<IconPlus />}>Add Item</Button>
<Button endIcon={<IconArrowRight />}>Next Step</Button>
```

Icon-only buttons should include an accessible label:

```jsx
<Button icon={<IconSettings />} aria-label="Settings" />
```

## Full Width

Buttons can expand to fill their container:

```jsx
<Button fullWidth>Full Width Button</Button>
```

## With Badge

For indicating counts or statuses:

```jsx
<Button badge={5}>Notifications</Button>
```

## Custom Styling

You can style the Button component using the `sx` prop for one-off customization:

```jsx
<Button 
  sx={{ 
    borderRadius: '2px', 
    backgroundColor: 'custom.brandColor',
    '&:hover': {
      backgroundColor: 'custom.brandColorDark',
    }
  }}
>
  Custom Styled Button
</Button>
```

## With Tooltip

Combine with the Tooltip component for additional context:

```jsx
import { Tooltip } from '@vibing-ai/block-kit';

<Tooltip content="This action cannot be undone">
  <Button variant="danger">Delete Account</Button>
</Tooltip>
```

## Button Group

Group related buttons with the ButtonGroup component:

```jsx
import { ButtonGroup } from '@vibing-ai/block-kit';

<ButtonGroup>
  <Button>Cancel</Button>
  <Button variant="primary">Submit</Button>
</ButtonGroup>
```

## As Different Element

Render as a different element while maintaining button functionality:

```jsx
<Button as="a" href="/dashboard">Go to Dashboard</Button>
```

## Props

| Prop | Type | Default | Description |
|------|------|---------|-------------|
| `variant` | `'primary' \| 'secondary' \| 'tertiary' \| 'ghost' \| 'link' \| 'danger'` | `'secondary'` | Button style variant |
| `size` | `'small' \| 'medium' \| 'large'` | `'medium'` | Button size |
| `disabled` | `boolean` | `false` | Disables the button |
| `loading` | `boolean` | `false` | Shows loading state with spinner |
| `active` | `boolean` | `false` | Shows button in active/selected state |
| `startIcon` | `ReactNode` | `undefined` | Icon displayed before button text |
| `endIcon` | `ReactNode` | `undefined` | Icon displayed after button text |
| `icon` | `ReactNode` | `undefined` | For icon-only buttons (requires aria-label) |
| `fullWidth` | `boolean` | `false` | Makes button take 100% of container width |
| `badge` | `number \| string` | `undefined` | Displays a badge on the button |
| `onClick` | `(event: React.MouseEvent) => void` | `undefined` | Click handler function |
| `as` | `React.ElementType` | `'button'` | Render as different element type |
| `sx` | `object` | `{}` | Style overrides using theme-ui style object |

Additional props are passed to the underlying element.

## Accessibility

- Buttons maintain a minimum touch target size of 44x44 pixels for mobile accessibility
- Focus states are clearly visible for keyboard navigation
- When using `as="a"`, proper aria attributes are automatically applied
- Icon-only buttons require an `aria-label` for screen reader accessibility
- Loading state is announced to screen readers with aria-busy

## Design Guidelines

- Use primary buttons for the main action in a section or page
- Limit the number of primary buttons per view to maintain visual hierarchy
- Place the primary button on the right in button groups for confirmation dialogs
- Use consistent button sizes within the same context or component
- Choose button variants that provide sufficient contrast against their background

## Examples

### Form Submit Button

```jsx
<form onSubmit={handleSubmit}>
  {/* Form fields */}
  <Button 
    variant="primary" 
    type="submit" 
    loading={isSubmitting}
    disabled={!isValid}
  >
    Submit Form
  </Button>
</form>
```

### Destructive Action With Confirmation

```jsx
function DeleteButton() {
  const [showConfirm, setShowConfirm] = useState(false);
  
  return (
    <>
      <Button 
        variant="danger" 
        onClick={() => setShowConfirm(true)}
      >
        Delete Account
      </Button>
      
      {showConfirm && (
        <Modal>
          <Text>Are you sure you want to delete your account? This action cannot be undone.</Text>
          <ButtonGroup>
            <Button onClick={() => setShowConfirm(false)}>Cancel</Button>
            <Button variant="danger" onClick={handleDelete}>Confirm Delete</Button>
          </ButtonGroup>
        </Modal>
      )}
    </>
  );
}
```

## Related Components

- [ButtonGroup](/component-library/basic/button-group) - For grouping related buttons
- [IconButton](/component-library/basic/icon-button) - Specialized component for icon-only buttons
- [Link](/component-library/basic/link) - For navigation without button styling 