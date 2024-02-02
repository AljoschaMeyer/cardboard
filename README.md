# Cardboard

Reading scientific papers could be a lot nicer than it currently is. Perhaps it is time to move on from papers... to cardbards. We propose a lightweight standard for creating interoperable, (primarily) textual units for transfer of knowledge in the scientific tradition, that adds expresivity and self-containment beyond what traditional papers can offer. More precisely:

- Cardboards are built with dynamic web technologies, they can include interactive elements.
- Cardboards are fully self-contained, they bundle the cardboards and papers they cite.

This feature set is quite modest, and requires little more than building a static website in adherence to a small set of conventions. While cardboards use web technologies, they are not inherently tied to the www: each cardboard is self-contained, so you can easily download it and view it locally (including everything it transitively references).

The resulting specification is generic enough to work not only for scientific documents, but for arbitrary self-contained content that wishes to reference (or be referenced by) other content.

## Specification

A *cardboard bundle* is a directory of the following structure:

- `pdfs`: A directory of non-cardboard references.
- `cardboards`: A directory of *cardboards*, containing all referenced cardboards and the entrypoint to this cardboard (defined later).

The `pdfs` directory contains legacy papers that any cardboard in the cardboard bundle cites directly. Each file in the `pdf` directory must be a pdf named `<digest>.pdf`, where `<digest>` is the lowercase-base-16 encoding of the sha256 hash of the pdf. This serves to deduplicate papers if they get cited by several cardboards in the same bundle.

The `cardboards` directory contains individual cardboards. Each *cardboard* is its own directory with a unique name, called the *cardboard id*. To ensure uniqueness, we recommend to include authors, a date, and title information in the id. The id must be an ascii string of alphanumeric characters or the underscore `_`, must be non-empty, the first character must not be a number, and it must consist of no more than 256 characters. Each cardboard directory must contain a `cardboard.json` file, an `index.html` file, as well as arbitrary further files.

The `cardboard.json` file supplies metadata about a cardboard. It must contain a single json object with the following fields (mandatory unless explicitly stated otherwise):

- `title`: A string, a human-readable title of the cardboard.
- `date`: A string giving the publication date of the cardboard as `YYYY-MM-DD`.
- `authors`: An array of strings, the authors of the cardboard. Use the empty array for anonymous authorship.
- `cardboards`: An array of dependency cardboard ids as strings. Each of these must be a directory in the `cardboards` directory. The array should contain no duplicates.
- `pdfs`: An array of dependency pdf names strings. Each of these must be a file in the `pdfs` directory. The array should contain no duplicates.

The object may contain arbitrary additional fields.

The `index.html` file is the entrypoint for viewing a cardboard. This file, and all other web technology files referenced from it, should assume that the root of the webserver seving it is one directory up. For example, an absolute link from `index.html` file to itself would be `/<CardboardId>/index.html`. The entry point of other cardboards can hence be linkted to via `/<OtherCardboardId>/index.html`. It is perfectly fine to use relative links as well, or to link to resources other than the `index.html` of a cardboard.

## Design Rationale

To have even the slightest chance of wider adoption, the specification has to be as simple as possible. That is why the feature set is the absolute bare minimum of what you need to switch from pdf to web technologies, to allow for self-contained bundling of all dependencies, and to ensure that bundles can be moved anywhere and read locally.

We expect individuals to keep a bundle of all the interesting cardboards they know of, including their own. When finding a new interesting bundle, it can simply be merged in by copying the contents of the `pdfs` and `cardboards` directories. If cardboard ids and sha256 digests uniquely identify their resources, than one can simply overwrite or ignore any duplicates.

For publishing a particular cardboard, you can create a minimal complete bundle by starting with the cardboard to publish, add all its dependency pdfs, and then recursively add all its dependency cardboards. The resulting bundle can be hosted on the web, and you then link to the `index.html` of the entrypoint cardboard.

It is easy to image simple software to make these workflows more convenient: a UI for managing one's "mega-bundle" — with the ability to merge in new bundles from a url or a zip file, with a tagging system for keeping cardboards organized, with a handy export button for creating minimal bundles for any cardboard, etc.

## Further Conventions

We can imagine several additional, useful features, that could be facilitated by adhering to further conventions. Some fun examples:

- Machine-readable specification of citations, so that user agents can freely select how to render them.
- Conventions for storing tooltips that could be loaded from other cardboards, similar to Wikipedia link previews.
- A canonic way for publishing newer versions of a cardboard, to allow for checking whether one reads an obsolete document.
- A canonic way for authors to list all their cardboards, to allow for discovery mechanisms.
- A canonic way for authors to offer peer-reviewing-like evaluation of other cardboards.
- etc etc etc

Such conventions are out of scope of the bare cardboard specification. It is up to those who wish to push these conventions forward to coordinate, specify, and promote them.