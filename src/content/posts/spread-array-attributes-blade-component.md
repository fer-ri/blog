---
title: Passing Array as Attributes in Blade Component
description: Effortlessly optimize attribute array integration in Blade components with streamlined prop passing.
publishDate: 2024-01-07
tags: [laravel, blade, php]
---

In React, we can efficiently pass an array or object of props to a component using the spread syntax, as illustrated in the following TypeScript example:

```ts
const attrs = {
    name: 'my-title',
    id: 'my-title',
    value: 'my-value'
}

<TextInput {...attrs} />

// The rendered output will resemble the following:
<input type="text" name="my-title" id="my-title" value="my-value" />
```

However, when working with PHP and Blade components, the native `spread` operator cannot be employed to pass an array of attributes directly to the component, as demonstrated in the following Blade example:

```blade
@php
$attrs = [
    'name' => 'my-title',
    'id' => 'my-title',
    'value' => 'my-value',
];
@endphp

// The following does not produce the expected outcome
<x-input [...$attrs] />
```

To overcome this limitation, we can utilize the `Illuminate\View\ComponentAttributeBag` in the Blade component:

```blade
<x-input :attributes="new \Illuminate\View\ComponentAttributeBag($attrs)" />
```

By doing so, within the component, direct access to attributes such as `$name`, `$id`, and `$value` becomes possible. This approach ensures a seamless integration of attribute arrays in Blade components.
