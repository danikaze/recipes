# TypeScript

- [ForbidFieldCollision](#forbid-field-collision)
- [SubType](#subtype)
- [PartialPick](#partial-pick)
- [GetMapKey](#get-map-key)
- [GetMapValue](#get-map-value)
- [ExcludeFromArray](#exclude-from-array)
- [KeysOfWithValue](#keysof-with-value)

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

## <a name="subtype"></a>SubType

```ts
/**
 * Checks that a `SubType` is properly extending a given `Type`
 */
type SubType<Type, SubType extends Type> = SubType extends Type
  ? SubType
  : never;
```

## <a name="partial-pick"></a>PartialPick

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

## <a name="get-map-key"></a>GetMapKey

```ts
/**
 * Returns the type used for a Map
 * GetMapKey<Map<K, V>> ==> K
 */
type GetMapKey<M> = M extends Map<infer K, infer V> ? K : never;
```

## <a name="get-map-value"></a>GetMapValue

```ts
/**
 * Returns the value used for a Map
 * GetMapValue<Map<K, V>> ==> V
 */
type GetMapValue<M> = M extends Map<infer K, infer V> ? V : never;
```

## <a name="exclude-from-array"></a>ExcludeFromArray

```ts
/**
 * Exclude from an array of type T elements that matches U
 *
 * @example
 * ExcludeFromArray<(number | string)[], string> === number[]
 */
type ExcludeFromArray<T extends any[], U> = T extends (infer D)[]
  ? Exclude<D, U>[]
  : never;
```

## <a name="keysof-with-value"></a>KeysOfWithValue

```ts
/**
 * Return the keys of an object O which values matches the type T
 */
type KeysOfWithValue<O extends {}, T> = Exclude<
  {
    [K in keyof O]: O[K] extends T ? K : never;
  }[keyof O],
  undefined
>;
```
