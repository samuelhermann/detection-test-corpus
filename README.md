# Detection Test Corpus (inert)

Authorized detection-testing samples. **No live malware here.** Every file carries an industry-standard *inert* test string that scanners are designed to flag:

- **EICAR** — standard antivirus test string. Harmless text; AV engines flag it by design.
- **GTUBE** — standard spam-filter test string (in the `.eml` samples).

Purpose: validate how EDR / AV / mail-gateway / upload pipelines handle disguised files and extension-vs-content mismatches.

## Layout

### `samples/single/` — one extension each
| File | Container | Tests |
|------|-----------|-------|
| `infiltration-test-script.txt` | raw EICAR | text-body scanning |
| `infiltration-test-page.html` | EICAR in HTML | web content scan |
| `infiltration-test-archive.gz` | gzip(EICAR) | archive unpacking |
| `infiltration-test-email.eml` | GTUBE + EICAR | mail-gateway / spam filter |
| `infiltration-test-image.jpg` | EICAR bytes, `.jpg` name | magic-byte vs extension mismatch |
| `infiltration-test-app.jar` | zip(EICAR) | JAR/zip unpacking |
| `infiltration-test-package.deb` | ar(EICAR) | deb/ar unpacking |

### `samples/double-ext/` — double extension, all ending `.jpg`
`infiltration-test.<real>.jpg` for `gz, eml, jar, deb, txt, html`. Tests whether the pipeline inspects true content/magic bytes rather than trusting the trailing `.jpg`.

### `samples/txt-ext/` — same payloads as `single/`, all renamed `.txt`
| File | Real content | Tests |
|------|--------------|-------|
| `infiltration-test-script.txt` | raw EICAR | baseline text scan |
| `infiltration-test-page.txt` | EICAR in HTML | content scan regardless of extension |
| `infiltration-test-archive.txt` | gzip(EICAR) | archive detection behind `.txt` |
| `infiltration-test-email.txt` | GTUBE + EICAR | mail content behind `.txt` |
| `infiltration-test-image.txt` | raw EICAR | text-body scanning |
| `infiltration-test-app.txt` | zip(EICAR) | zip/JAR detection behind `.txt` |
| `infiltration-test-package.txt` | ar(EICAR) | ar/deb detection behind `.txt` |

Tests whether the pipeline still unpacks/scans binary containers when the extension claims plain text.

### `samples/obfuscated-html/` — every `single/` payload, obfuscated in HTML
One HTML file per `single/` payload. Each embeds that container's **real bytes** as base64 and reassembles them client-side (`atob` → `Uint8Array` → `Blob`), so a scanner that decodes the page sees the genuine gzip/zip/ar/eml/EICAR, not just a literal string. Tests whether the stack decodes embedded/base64 content inside HTML.
| File | Reconstructs | Tests |
|------|--------------|-------|
| `infiltration-test-script.txt.html` | raw EICAR | base64-in-HTML decode |
| `infiltration-test-page.html.html` | EICAR-in-HTML | nested HTML decode |
| `infiltration-test-archive.gz.html` | gzip(EICAR) | decode + archive unpack |
| `infiltration-test-email.eml.html` | GTUBE + EICAR | decode + mail scan |
| `infiltration-test-image.jpg.html` | raw EICAR (`.jpg`) | decode of disguised bytes |
| `infiltration-test-app.jar.html` | zip(EICAR) | decode + zip unpack |
| `infiltration-test-package.deb.html` | ar(EICAR) | decode + ar unpack |

## Expected result
A working detection stack flags **every** file. Anything that passes clean = a gap to investigate.

## Safety
Inert test artifacts only. Do not use to validate anything other than detection coverage on systems you are authorized to test.
