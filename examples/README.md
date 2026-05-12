# Examples

This directory is reserved for real Lanhu-to-code examples.

Recommended examples to add:

1. `vue-order-page/`
   - Lanhu input screenshot or redacted design summary
   - generated Vue page code
   - reused components list
   - TODO list

2. `react-dashboard-page/`
   - Lanhu input screenshot or redacted design summary
   - generated React page code
   - reused components list
   - TODO list

Each example should answer four questions:

- What Lanhu design was used?
- What files did Claude Code generate or modify?
- Which existing components were reused?
- Which API, enum, or business logic items were marked as TODO?

Suggested structure:

```text
examples/vue-order-page/
├── README.md
├── generated/
│   └── src/views/order/index.vue
└── screenshots/
    ├── lanhu-input.png
    └── generated-page.png
```
