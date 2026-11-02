# Reusable blocks

Blocks hold small, structured pieces of content that a theme can reuse without turning them into pages. Typical examples include a call to action, profile card, contact details, sponsor panel, or a set of footer links.

## Structure

```text
block group
  block
    element
    element
  block
    element
```

A group has a name and handle. A block belongs to one group and has its own handle and description. Elements belong to a block and have a handle, type, and value.

## Handles are the template contract

Handles let a template request content by meaning rather than database ID. For example:

```text
profile.name
profile.website
profile.photo
```

Block handles are globally unique. Element handles are unique within their block. Use lowercase, descriptive names and treat a published handle like an API name. Renaming it requires a matching theme change.

## Element types

| Type | Good use | Validation and output |
| --- | --- | --- |
| `text` | Names, labels, short statements | Plain text |
| `rich_text` | A short formatted description | Compact sanitized HTML |
| `link` | Internal path or external URL | Validated link value |
| `image` | Logo, portrait, badge, decorative asset | Media record in the `blocks` collection |

Compact rich text accepts paragraphs, line breaks, emphasis, links, and lists. Links open in a new tab and receive `nofollow noopener noreferrer` through the sanitizer.

## Editing a block

1. Create or choose a block group.
2. Add a block with a stable handle and a description that explains where it appears.
3. Add elements in the order expected by the editing interface.
4. Give each element a clear handle and choose its type.
5. Save and check every theme location that consumes the block.

Block saves use a transaction so element changes and the block record remain consistent. Image elements reference managed media. Replaced or removed images are later reconciled by the orphan cleanup job.

## Reading blocks in a theme

The block service supports two patterns:

- request one value by its `block.element` path, with an optional default;
- request a complete block group and work with its structured block objects.

The single-value form is convenient for small template values. A complete group is better when a component repeats or needs several related fields.

## Deletion rules

A group that still contains blocks should not be treated as an empty container. Before deleting a block or group, identify the theme references to its handles. Database constraints protect parts of the relationship, but they cannot detect a handle embedded in a PHP template.

Block image files are not deleted inline with every edit. The daily orphan cleanup compares media references and removes files that no longer belong to a valid block element.

## Design guidance

- Keep a block focused on one visual or editorial responsibility.
- Prefer several clear fields to one large rich-text field when the theme needs structure.
- Put long, navigable content in a page instead.
- Write descriptions for future editors, including where the block appears.
- Keep presentation choices in the theme. A block should describe content, not CSS.
