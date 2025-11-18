# Component Composition

Build complex UIs by composing simple, focused components.

## Pattern

Create small, reusable components and combine them:

```javascript
// Atomic components
function Button({ children, onClick, variant = 'primary' }) {
  return (
    <button className={`btn btn-${variant}`} onClick={onClick}>
      {children}
    </button>
  );
}

function Card({ children, title }) {
  return (
    <div className="card">
      {title && <h3>{title}</h3>}
      <div className="card-content">{children}</div>
    </div>
  );
}

function Avatar({ src, name, size = 'medium' }) {
  return (
    <img
      src={src}
      alt={name}
      className={`avatar avatar-${size}`}
    />
  );
}

// Composed component
function UserCard({ user, onEdit }) {
  return (
    <Card title={user.name}>
      <Avatar src={user.avatar} name={user.name} />
      <p>{user.bio}</p>
      <Button variant="primary" onClick={onEdit}>
        Edit Profile
      </Button>
    </Card>
  );
}
```

## Children Prop Pattern

Use `children` for flexible composition:

```javascript
function Modal({ children, isOpen, onClose }) {
  if (!isOpen) return null;

  return (
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={e => e.stopPropagation()}>
        {children}
      </div>
    </div>
  );
}

// Usage
function App() {
  return (
    <Modal isOpen={isOpen} onClose={closeModal}>
      <h2>Confirm Delete</h2>
      <p>Are you sure?</p>
      <Button onClick={confirmDelete}>Yes</Button>
    </Modal>
  );
}
```

## Compound Components

Create components that work together:

```javascript
function Tabs({ children, activeTab, onChange }) {
  return (
    <div className="tabs">
      {React.Children.map(children, (child, index) =>
        React.cloneElement(child, {
          isActive: index === activeTab,
          onClick: () => onChange(index),
        })
      )}
    </div>
  );
}

function Tab({ label, isActive, onClick }) {
  const class_name = isActive ? 'tab active' : 'tab';
  return (
    <button className={class_name} onClick={onClick}>
      {label}
    </button>
  );
}

// Usage
<Tabs activeTab={0} onChange={setActiveTab}>
  <Tab label="Profile" />
  <Tab label="Settings" />
  <Tab label="Billing" />
</Tabs>
```

## Benefits

- **Reusability**: Small components used in many places
- **Testability**: Easy to test small components
- **Maintainability**: Changes isolated to specific components
- **Readability**: Self-documenting component hierarchy

## Related Notes
- [Container/Presentational Pattern](./container-presentational-pattern.md)
- [Component Files Structure](./component-files.md)
- [Props Destructuring](./props-destructuring.md)
