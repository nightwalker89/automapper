<p align="center"><img src="https://avatars1.githubusercontent.com/u/43827489?s=400&u=45ac0ac47d40b6d8f277c96bdf00244c10508aef&v=4"/></p>
![travis](https://badgen.net/travis/nestjsx/automapper)
![bundlephobia](https://badgen.net/bundlephobia/minzip/nestjsx-automapper)
![downloads](https://badgen.net/npm/dt/nestjsx-automapper)
![npm](https://badgen.net/npm/v/nestjsx-automapperr)
![license](https://badgen.net/github/license/nestjsx/automapper)
[![Coverage Status](https://coveralls.io/repos/github/nestjsx/automapper/badge.svg?branch=master)](https://coveralls.io/github/nestjsx/automapper?branch=master)
<h1 align="center">Nest Automapper</h1>
<p align="center">A wrapper around <a href="https://github.com/nartc/mapper">@nartc/automapper</a> to be used with <strong>NestJS</strong> as a <code>Module</code>.</p>

## Documentations

This module is a wrapper around `@nartc/automapper` so all usage documentations should be referenced at the link below. 

Github Pages [https://nartc.github.io/mapper/](https://nartc.github.io/mapper/)
Github Repo [https://github.com/nartc/mapper](https://github.com/nartc/mapper)

## Features

- [x] Mapping between two classes
- [x] Mapping for nested classes
- [x] Array/List Mapping
- [x] Flattening
- [x] Basic ReverseMap
- [x] Value Converters
- [x] Value Resolvers
- [x] Async
- [x] Before/After Callback
- [x] Naming Conventions (PascalCase and camelCase)

#### Future features:

- [ ] Type Converters - Help needed
- [ ] Value Transformers

#### Might not support / Need use-case:

- [x] Null Substitution - It makes more sense to use `fromValue()` instead of implement `nullSubstitution()`. Please let me know of a use-case where `nullSubstitution()` makes sense.

Contributions are appreciated.


## Setup
```
npm i -s nestjsx-automapper
```

Installing `nestjsx-automapper` will also install `@nartc/automapper`.

**Note 1**: Please make sure that you've read `@nartc/automapper` documentations to familiarize yourself with `AutoMapper`'s terminology and how to setup your `Profile` and such.

1. Import `AutomapperModule` in `AppModule` and call `.forRoot()` method.

```typescript
@Module({
  imports: [AutomapperModule.forRoot()]
})
export class AppModule {}
```
 
`AutomapperModule.forRoot()` method expects an `AutomapperModuleRootOptions`. When you call `AutomapperModule.forRoot()`, a new instance of `AutoMapper` will be created with the `name` option. There are two properties on the options that you can pass in:
- `name`: Name of this `AutoMapper` instance. Default to `"default"`.
- `config`: A configuration function that will get called automatically.

Both options are optional. If you pass in `config` and configure your `AutoMapper` there, that is totally fine, but the following approach is recommended. Refer to [@nartc/automapper: usage](https://github.com/nartc/mapper#usage) 

2. `AutoMapper` has a concept of `Profile`. A `Profile` is a class that will house some specific mappings related to a specific domain model. Eg: `User` mappings will be housed by `UserProfile`. Refer to [@nartc/automapper: usage](https://github.com/nartc/mapper#usage) for more information regarding `Profile`.

`NestJS` recommends you to separate features/domains in your application into `Modules`, in each module you would import/declare other modules/parts that are related to that Module. `AutomapperModule` also has a static method `forFeature` which should be used in such a feature module. `forFeature` accepts an `AutomapperModuleFeatureOptions` which has:
- `profiles`: An array of `Profiles` related to this module, and this will be added to an `AutoMapper` instance.
- `name`: Decide which `AutoMapper` instance to add these profiles to. Default to `"default"`

```typescript 
@Module({
  imports: [AutomapperModule.forFeature({profiles: [new UserProfile()]})]
})
export class UserModule {}
```

3. Inject an instance of `AutoMapper` in your `Service`:

```typescript
export class UserService {
  constructor(@InjectMapper() private readonly _mapper: AutoMapper) {}
}
```

**Note**: `AutoMapper` is imported from `@nartc/automapper`. `InjectMapper` decorator is imported from `nest-automapper`.

`InjectMapper()` accepts an optional argument `name` which will tell the decorator to inject the right instance of `AutoMapper`. Default to `"default"`.

4. Use `AutoMapper` on your domain models:

```typescript
...
const result = await newUser.save();
return this._mapper.map(result.toJSON(), UserVm);
...
```
