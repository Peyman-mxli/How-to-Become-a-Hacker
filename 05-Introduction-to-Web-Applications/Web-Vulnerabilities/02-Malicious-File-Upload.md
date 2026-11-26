# Malicious File Upload — Defensive Study Guide

An upload feature accepts untrusted bytes and metadata. A vulnerability occurs when those bytes can violate security policy, for example by being executed as server code, overwriting files, exposing private documents, or consuming excessive resources. Extension, client MIME header and original filename are not trusted proof of file type. A multiple-extension filename may expose inconsistent validation and handler rules; it does not automatically cause execution. The supplied course references a historical WordPress Responsive Thumbnail Slider issue. This guide does not reproduce exploit instructions.

## Secure processing pipeline
Authenticate and authorize -> enforce request and file limits -> allowlist supported formats -> decode and validate content -> enforce dimensions -> re-encode safe formats -> generate independent filename -> store outside web root -> authorized retrieval.

## Original image validation example
Requires Pillow and an existing private directory controlled by the service. Accept only already size-limited input; enforce HTTP and decoded image limits separately.
```python
from pathlib import Path
from uuid import uuid4
from PIL import Image, ImageOps

def save_avatar(stream, private_dir: Path):
    with Image.open(stream, formats=["JPEG", "PNG"]) as image:
        if image.width * image.height > 12_000_000:
            raise ValueError("Image dimensions too large")
        image.load()
        clean = ImageOps.exif_transpose(image).convert("RGB")
        clean.thumbnail((1024, 1024))
        name = uuid4().hex + ".jpg"
        clean.save(private_dir / name, format="JPEG", quality=85)
    return name
```
Image.open selects permitted decoders; dimensions bound pixel count; load decodes data; exif_transpose handles orientation; convert and thumbnail create a controlled image; uuid4 creates a new name; save re-encodes pixels. Do not expose parser exceptions to clients. Keep Pillow patched; use process resource limits and isolation as appropriate. The function alone is not a full upload handler: authentication, CSRF, quota, safe directory permissions, cleanup, error handling and authorized retrieval are required.

## Defensive tests
Use benign fixtures on owned projects: a valid JPEG, a text document renamed .jpg, an oversized file, a malformed image, and a filename with multiple suffixes. Check that only valid content is stored and returned to authorized callers; no uploaded directory may execute scripts. Test rejection without malicious code. UniTrack visitor and credential photos and ProTrack-MX attachments need separate access policies, storage rules and size limits.

References: [OWASP File Upload](https://cheatsheetseries.owasp.org/cheatsheets/File_Upload_Cheat_Sheet.html), [Pillow](https://pillow.readthedocs.io/). No live tests performed.

## Expanded terminology and validation reference

| Term | Meaning |
| --- | --- |
| File extension | Filename suffix, such as .jpg |
| MIME/media type | Declared format metadata |
| File signature | Characteristic initial bytes; not proof of complete validity |
| Double extension | Filename containing multiple suffixes |
| Arbitrary file upload | Upload outside the intended policy |
| Web root | Directory from which a web server serves public resources |
| Web shell | Server-side script offering unauthorized remote management or command access |
| File validation | Checking content, format, limits and policy |

| Benign test fixture | Expected behavior for an image-only upload |
| --- | --- |
| Valid JPEG within limits | Accept, decode and re-encode |
| Plain text named photo.jpg | Reject |
| File with multiple suffixes | Reject or independently validate according to explicit policy |
| Valid PDF | Reject if PDF is not allowed |
| Corrupted PNG | Reject |
| Image exceeding dimension/byte limits | Reject |

A client-provided MIME type and an extension must not determine whether content is executed. Even a valid image can violate privacy or contain metadata. Storage must be non-executable and retrieval authorized.

### Image example reference

| Component | Purpose |
| --- | --- |
| pathlib.Path | Filesystem path representation |
| uuid4 | Generate independent random identifier |
| Pillow / PIL | Image decoder and processing library |
| Image.open(formats=...) | Restrict allowed decoder formats |
| image.width, image.height | Check decoded image dimensions |
| image.load | Decode pixels |
| ImageOps.exif_transpose | Correct orientation |
| convert("RGB") | Normalize color mode |
| thumbnail | Bound output dimensions |
| save(format="JPEG") | Encode controlled output format |

Treat decoder errors as invalid uploads without revealing internal paths. Keep private directory under service control; use safe write/cleanup behavior. Strip unnecessary metadata, apply byte quotas, and use content-disposition and content-type policies on download.
