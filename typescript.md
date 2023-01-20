# TypeScript

- [ForbidFieldCollision](#forbid-field-collision)

## <a name="forbid-field-collision"></a>ForbidFieldCollision

```ts
/**
 * Returns the union of the interfaces A and B (same as `A & B`)
 * but guaranteeing there are no field collisions between them
 */
type ForbidFieldCollision<
  A,
  B extends Record<string, any> & Partial<Record<keyof A, never>>
> = A & B;
```

```ts
/**
 * Checks that a `SubType` is properly extending a given `Type`
 */
type SubType<Type, SubType extends Type> = SubType extends Type
  ? SubType
  : never;
```

```ts
/**
 * Converts some `Keys` of a given `Interface` to optional
 */
type PartialPick<Interface extends {}, Keys extends keyof Interface> = Omit<
  Interface,
  Keys
> &
  Partial<Pick<Interface, Keys>>;
```

```ts
/**
 * Returns the type used for a Map
 * GetMapKey<Map<K, V>> ==> K
 */
type GetMapKey<M> = M extends Map<infer K, infer V> ? K : never;
```

```ts
/**
 * Returns the value used for a Map
 * GetMapValue<Map<K, V>> ==> V
 */
type GetMapValue<M> = M extends Map<infer K, infer V> ? V : never;
```
