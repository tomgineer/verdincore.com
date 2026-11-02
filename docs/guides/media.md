# Media

VerdinCore stores media metadata in MariaDB and image files under `public/images/`. Processing depends on the collection used by the upload endpoint.

## Collections

| Collection | Purpose | Processing |
| --- | --- | --- |
| `featured_post` | Post cards, post headers, social previews | Original AVIF plus fixed crop variants and a JPEG share image |
| `featured_page` | Page presentation and social previews | Original AVIF plus page-specific crop variants and a JPEG share image |
| `content` | Images inserted into rich content | Original AVIF plus proportional fit variants |
| `blocks` | Logos and other structured block assets | Accepted original file, without resizing or conversion |

## Generated sizes

### Featured post

| Variant | Size | Format |
| --- | --- | --- |
| Small | 450 x 253 | AVIF |
| Medium | 976 x 549 | AVIF |
| Large | 1952 x 1098 | AVIF |
| Share | 1200 x 630 | JPEG |

### Featured page

| Variant | Size | Format |
| --- | --- | --- |
| Small | 768 x 896 | AVIF |
| Medium | 1440 x 960 | AVIF |
| Large | 2560 x 1080 | AVIF |
| Share | 1200 x 630 | JPEG |

### Inline content

| Variant | Bounding size | Format |
| --- | --- | --- |
| Small | 512 x 512 | AVIF |
| Medium | 1024 x 1024 | AVIF |
| Large | 2048 x 2048 | AVIF |

Featured variants fill their exact dimensions with a centered crop. Content variants scale to fit their bounds and preserve the image ratio.

## Upload validation

The image configuration allows uploads up to 20,480 KB and dimensions up to 12,000 x 12,000 pixels. The pipeline checks the detected media type instead of trusting the client filename.

Generated collections require image formats the processor can decode and convert. The `blocks` collection accepts JPEG, PNG, WebP, AVIF, and SVG in the current configuration.

Files receive a cryptographically random 32-character hexadecimal basename. The original filename remains in metadata for administration, but it is not used as the public storage path.

## Processing flow

1. Validate the upload and collection.
2. Read image dimensions and apply EXIF orientation where supported.
3. Generate the collection's output files.
4. Save the media metadata record.
5. Return public URLs for the stored variants.

If processing or persistence fails, the uploader removes incomplete output. Storage path validation also rejects unexpected collection names and filenames before deletion.

## Metadata

Each media record can include:

- uploader;
- original filename;
- generated filename;
- collection;
- alternative text;
- caption;
- created and updated times.

Alternative text should explain the image's purpose in context. Leave it empty only when the image is decorative and the consuming template handles that case correctly.

## Cleanup

The daily maintenance run checks three orphan categories:

- featured images no longer referenced by a post or page;
- inline content images no longer present in stored content;
- block images no longer referenced by an image element.

Cleanup removes both the database record and the files that belong to the collection. Keep database and `public/images/` backups synchronized so restoration does not produce broken references.

## Server requirements

The default pipeline uses GD and AVIF. The PHP build must provide `imageavif()` and `imagecreatefromavif()`. The PHP process also needs write access to the collection directories under `public/images/`.
