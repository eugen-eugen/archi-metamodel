# @eugen-eugen/archi-metamodel

jArchi meta-model library for [Archi](https://www.archimatetool.com/) / the
[jArchi](https://github.com/archimatetool/archi-scripting-plugin) scripting plugin.

A **meta-model** defines which relationships are allowed between which types of
ArchiMate elements. This library extracts a meta-model from an Archi view and lets
you match real elements/relationships against it, honoring
specialization/generalization inheritance, and read visual formatting from the
meta-model.

> **Runtime:** This library targets the jArchi environment. It relies on the
> jArchi globals `$` (the jArchi query API) and `console`, which Archi injects at
> runtime. It is meant to be bundled into a jArchi script (`.ajs`) via a build
> step (webpack/esbuild), not executed under plain Node.

## Meta-model definition

A meta-model is discovered from a view in one of two ways:

1. A visual **group** with a property `meta` on the current view, or
2. A **view reference** on the current view pointing to another view that has the
   `meta` property. In that case, all elements on the referenced view form the
   meta-model.

Specialization handling:

- When a real element matches a meta-model element, it also matches all ancestors
  (generalizations) in the specialization hierarchy.
- Relationships defined on generalized elements are inherited by specialized ones.
- Cycles in specialization relationships are detected to prevent infinite loops.

## Install

Published to GitHub Packages under the `@eugen-eugen` scope. Add to your project's
`.npmrc`:

```
@eugen-eugen:registry=https://npm.pkg.github.com
```

Then:

```sh
npm install @eugen-eugen/archi-metamodel
```

## Usage

Require it from a script that is bundled into a `.ajs` file:

```js
const {
  extractMetaModel,
  findMetaModelMatches,
  isRelationshipAllowed,
} = require("@eugen-eugen/archi-metamodel");

const metaModel = extractMetaModel(view);
if (metaModel) {
  const matches = findMetaModelMatches(element, metaModel);
  // ...
}
```

## API

- `extractMetaModel(view)` — Extracts the meta-model from a view; returns `null` if none found.
- `findMetaModelMatches(element, metaModel)` — Finds matching meta-model elements for a real element.
- `findMetaModelRelationshipMatches(relationship, metaModel)` — Finds matching meta-model relationships.
- `isRelationshipAllowed(source, target, rel, metaModel)` — Validates a relationship against the meta-model.
- `getMetaModelFormatting(element, metaView)` — Reads visual formatting props (font, alignment, label expression) for an element.
- `getMetaModelRelationshipFormatting(relationship, metaView)` — Reads visual formatting props for a relationship.
- `applyFormatting(visualElement, formatting)` — Applies formatting props to a visual object.

## License

MIT © Yevgen Moldawski
