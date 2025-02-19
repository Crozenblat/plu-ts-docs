# structs

When writing programs we often need to access data that is more articulate than simple integers or booleans; for this reason `plu-ts` allows ytou to define custom data-types.

## `pstruct`

To define your own type all you need is the `pstruct` ts function.

`pstruct` takes as an argument an object that describes the structure of the new data-type and returns a Typescript class that represents our new type.

The type of a struct definition (which is the argument of `pstruct`) is something like:

```ts
type StructDefiniton = {
    [constructor: string]: {
        [field: string]: TermType
    }
};
```

From this type we can already see that a struct can have *multiple constructors* (at least one) and each constructor can have 0 or more named fields.

This characteristic of having multiple constructors will allow for the creation of *custom control flows* through the use of [`pmatch` described in its own section](../control_flow/pmatch.md).

For now let's focus on defining some new structs and say we wanted to define a datatype that describes a Dog.

We could do so by writing:
```ts
// structs with single constructors acts in a similar way of plain typescript object
const Dog = pstruct({
    // single constructor
    Dog: {
        name: str,
        age: Age.type
    }
});
```

but our dog needs some toys to play with when we are out. So we define a structure that describes some toys:

```ts
const Toy = pstruct({
    Stick: {},
    Ball: {
        size: int,
        isSoft: bool
    },
    Mailman: {
        name: str,
        age: Age.type
    }
})
```
So now we can add a new field to better describe our dog:
```ts
const Dog = pstruct({
    Dog: {
        name: str,
        age: Age.type,
        favouriteToy: Toy.type
    }
});
```

> ## IMPORTANT
>
> When defining a struct the order of the constructors and the order of the fileds matters
>
> infact at UPLC level there are no names
>
> this do have to important implication
>
> 1) structs with similar definition will be interchangeable; meaning that something like
>       ```ts
>       const Kid = pstruct{
>          Kid: {
>               name: str,
>               age: Age.type,
>               toy: Toy.type
>           }
>       }
>       ```
>       can be used in place of a `Dog` without anything breaking
>
> 2) **changing the order** of constructors or fields **gives back a totally different struct**


## struct values

To build a `plu-ts` value that represents a struct you just use one of the constructors you defined.

So if you where to create an instance of a `Dog` you'd just write:
```ts
const myDog = Dog.Dog({

    name: pStr("Pluto"),
    age:  Age.from( pInt(3) ),

    favouriteToy: Toy.Mailman({
        name: pStr("Bob"),
        age:  Age.from( pInt(42) )
    })

})
```

## struct `plu-ts` type

Like [aliases](./aliases.md), structs also do have custom `plu-ts` types; which can be accessed using the `type` static property

```ts
const plutsTypeOfToy = Toy.type;
```