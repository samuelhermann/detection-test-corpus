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

## Expected result
A working detection stack flags **every** file. Anything that passes clean = a gap to investigate.

## Safety
Inert test artifacts only. Do not use to validate anything other than detection coverage on systems you are authorized to test.
