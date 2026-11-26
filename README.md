# beA-API-PYTHON Version 3.0

> Dieses beA-Python-Script ermöglicht es Ihnen, den Zugriff auf das beA in Ihre Kanzleisoftware zu integrieren. Sie können das Abholen und Versenden von beA-Nachrichten in Ihrer Software und somit auch an Ihren Workflow anpassen. Wir stellen ca. 40 Funktionen rund um das beA-Postfach mit der entsprechenden Dokumentation und Beispielsoftware bereit.

Proof of concept (ForlderOverview of a beA-Account/INBOX just using a valid Softwaretoken): [console.bea.expert](https://console.bea.expert/)

To use this script you need first to subscribe to a beA.expert API-Account. 
For more information: [bea.expert/api/](https://bea.expert/api/)

## New in version 3.0

| Function | Purpose |
|---|---|
| `bea_get_userjournal` | journal of a user (identity SAFE-ID) |
| `bea_get_message_pruefprotokoll` | "Prüfprotokoll" of a message as PDF |
| `p7s_digest_algorithm` | hash algorithm (`sha256`, `sha384`, `sha512`, ...) used by a `.p7s` / `.pkcs7` signature |
| `bea_get_gericht_codes` | codes of the courts for `msg_infos.gericht_code` (code list GDS.Gerichte) |
| `qes_get_sigtoken` | qualified electronic signature (qeS): sigtoken, devices and certificates of the user |
| `qes_sign_file` | qeS: start the signature of one or more files |
| `qes_get_signature` | qeS: poll the state of the signature, fetch the signatures |

**Removed:** `bea_get_addressbook` - deprecated, the API answers "bea_get_addressbook deprecated".

Tested on 26.11.2026 against the beA test environment together with the three other libraries: every function except the address book functions, the qeS functions and the login without signature (`bex_login`), including a message sent from each library and read by each of the others.

**Prüfprotokoll:** the message has to be available in the beA postbox. Attachments with a signature file (`name.pdf` + `name.pdf.p7s` or `.pkcs7`) are hashed with the algorithm of their signature (first signer), all other attachments with SHA-256. The request field is still called `sha256`; the algorithm is sent in `digest_alg`.

**qeS:** call `qes_get_sigtoken`, then `qes_sign_file` with one device and one certificate of its result and the files (`file_name`, `file_b64`, `file_sha256_hexhash`). The user confirms the signature on the device; poll `qes_get_signature` with the `id` of the transaction (e.g. every 2 seconds) until signatures or an error are returned. Every response contains a new `token`: always continue with the token of the last response.

Other changes: the login signature is created with `openssl cms -sign -binary` (the challenge is signed byte for byte).

The answer of `bea_get_gericht_codes` carries no token: the function returns the token it was called with.

## Dependencies

```
pip install requests pycryptodome pycryptodomex cryptography asn1crypto
```

`asn1crypto` is new in version 3.0 (hash detection for the Prüfprotokoll). The `openssl` command line tool is needed for the login.

## Licence

GPL-2.0 or BSD-3-Clause, at your option: see `LICENSE`.
