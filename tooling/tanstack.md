# TanStack

## TanStack Query

The default solution for server state management. Replaces manual `useEffect` + `useState` data fetching entirely.

### Key Patterns

```ts
// Query
const { data, isLoading, error } = useQuery({
  queryKey: ["invoices", filters],
  queryFn: () => fetchInvoices(filters),
  staleTime: 30_000,       // data stays fresh for 30s
  gcTime: 5 * 60 * 1000,  // cache kept for 5 minutes after unmount
});

// Mutation with optimistic update
const { mutate } = useMutation({
  mutationFn: createInvoice,
  onMutate: async (newInvoice) => {
    await queryClient.cancelQueries({ queryKey: ["invoices"] });
    const previous = queryClient.getQueryData(["invoices"]);
    queryClient.setQueryData(["invoices"], (old: Invoice[]) => [...old, newInvoice]);
    return { previous };
  },
  onError: (_, __, context) => {
    queryClient.setQueryData(["invoices"], context?.previous);
  },
  onSettled: () => {
    queryClient.invalidateQueries({ queryKey: ["invoices"] });
  },
});
```

### Query Key Conventions

Use arrays. Nest from general to specific:

```ts
["invoices"]                    // all invoices
["invoices", { status: "open" }] // invoices with filters
["invoices", "123"]             // specific invoice
["invoices", "123", "comments"] // nested resource
```

### Setup

```tsx
// app/providers.tsx
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 60_000,
      retry: 1,
    },
  },
});

export function Providers({ children }: { children: React.ReactNode }) {
  return (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  );
}
```

---

## TanStack Router

Type-safe routing with first-class search param support.

### Key Patterns

```ts
// Typed search params
const invoiceRoute = createRoute({
  path: "/invoices",
  validateSearch: z.object({
    status: z.enum(["all", "open", "paid"]).default("all"),
    page: z.number().int().positive().default(1),
  }),
});

// Using search params in a component
const { status, page } = useSearch({ from: "/invoices" });
const navigate = useNavigate();

const setStatus = (status: string) =>
  navigate({ search: (prev) => ({ ...prev, status }) });
```

---

## TanStack Form

Type-safe form state management with schema validation.

### Key Patterns

```tsx
const form = useForm({
  defaultValues: { title: "", amount: 0 },
  validatorAdapter: zodValidator(),
  validators: { onChange: createInvoiceSchema },
  onSubmit: async ({ value }) => {
    await createInvoice(value);
  },
});
```

See [react/forms.md](../react/forms.md) for full examples.

---

## DO NOT

- Mix TanStack Query with manual `useEffect` fetch patterns
- Store server state in Zustand or Context
- Use TanStack Query for pure local UI state

## See Also

- [`../react/state-management.md`](../react/state-management.md) — state hierarchy
- [`../react/forms.md`](../react/forms.md) — TanStack Form patterns
- [`../architecture/api-design.md`](../architecture/api-design.md) — API client patterns
- [`../react/use-effect.md`](../react/use-effect.md) — why not useEffect for fetching
