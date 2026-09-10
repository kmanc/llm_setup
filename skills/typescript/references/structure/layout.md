#### Example layout
```text

src/
├── auth/            # Domain module
│   ├── index.ts     # Public surface of the module
│   ├── token.ts
│   └── session.ts
├── orders/          # Domain module
│   ├── index.ts
│   ├── model.ts
│   └── service.ts
└── db/              # Infrastructure
    └── client.ts
```