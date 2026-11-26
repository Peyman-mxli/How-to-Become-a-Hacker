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
