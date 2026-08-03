# Aarhi Impact Foundation — Certificate Verification System

Adapted from Khyontek AI's Certificate System v3.

## Features
- Admin panel (in the separate `aif-programmes` repo) with confirmation modal before issuing
- Up to 3 collaborator logos on certificates
- Dynamic signature block (NJK + Alakesh Sarmah, both Directors)
- Collaborator signatory support
- Custom track name
- Payment tracking
- Resend button on the public verify page
- Admin email notification on every verification
- Duplicate student check
- Watermark background design (DNA, neural network, Assam motifs)

## Structure
```
aif-programmes-gen/
├── index.html                     ← verify.aarhiimpactfoundation.org (standalone verify page)
├── api/verify.js                  ← Vercel API backing index.html — verify, resend, notify admin
├── scripts/
│   ├── generate_cert.py           ← PDF generator, called by issue-certificate.yml
│   ├── mongo_insert.py            ← MongoDB logging no-op (record already saved by aif-programmes)
│   ├── send_email.py              ← Resend email, called by issue-certificate.yml
│   ├── archive_cert.py            ← Optional: archive PDF to a GitHub repo. NOT called by the
│   │                                 workflow — run manually, or wire it in if you want archiving.
│   ├── bulk-insert.js             ← Manual local CLI: bulk CSV insert directly to MongoDB.
│   │                                 The admin portal's own /admin/certificates/bulk page is the
│   │                                 normal way to do this — this script is a fallback/direct-DB path.
│   ├── insert-certificate.js      ← Manual local CLI: insert one certificate record directly.
│   │                                 Same caveat as above — normal path is the admin portal.
│   └── fonts/
│       ├── logo.png               ← AIF logo (in place)
│       ├── *.ttf                  ← Font files
│       ├── sig_njk.png            ← NJK signature (in place)
│       ├── sig_alakesh.png        ← Alakesh Sarmah signature (in place)
│       └── collab-logos/          ← Collaborator logos go here
│           └── README.md
├── .github/workflows/
│   └── issue-certificate.yml      ← The only workflow. Triggered by repository_dispatch
│                                     from aif-programmes on every certificate issuance.
├── vercel.json
├── package.json
├── .gitignore
└── README.md
```

**Not in this repo:** `admin.html` and the GitHub-Pages-based standalone admin form that used to
live here were removed — they were an earlier prototype, superseded by the full admin portal in
`aif-programmes` (`/admin/certificates`). That portal has proper server-side auth (JWT + bcrypt)
instead of credentials baked into a static page, and doesn't have the `issue_certificate` vs
`issue-certificate` event-type bug the old form had. `build_admin.py`, which only existed to
inject secrets into that removed page, was removed with it.

## GitHub Secrets Required (11 total — for issue-certificate.yml)
| Secret | Value |
|---|---|
| MONGODB_URI | MongoDB Atlas connection string |
| DB_NAME | aarhi_certs |
| EMAIL_SALT | Random string — must match Vercel exactly, never change once a cert is issued |
| RESEND_API_KEY | Resend API key |
| ADMIN_EMAIL | Admin notification address |
| R2_ACCOUNT_ID | Cloudflare R2 account ID |
| R2_ACCESS_KEY_ID | R2 API access key |
| R2_SECRET_ACCESS_KEY | R2 API secret key |
| R2_BUCKET_NAME | aarhi-programmes |
| CERT_DELIVERY_SECRET | Random string — must match the `aif-programmes` Vercel env var |
| BASE_URL | https://programmes.aarhiimpactfoundation.org |

## Vercel Environment Variables (for index.html + api/verify.js, if deployed separately)
| Variable | Value |
|---|---|
| MONGODB_URI | Same as GitHub secret |
| DB_NAME | aarhi_certs |
| EMAIL_SALT | Same as GitHub secret |
| RESEND_API_KEY | Same as GitHub secret |
| ADMIN_EMAIL | certificates@aarhiimpactfoundation.org |

## Certificate ID Format
AIF-[PROG]-[YY][SERIAL]
Example: AIF-INT-260001

## Key URLs
- Verify: https://verify.aarhiimpactfoundation.org
- Admin (full portal, separate repo): https://programmes.aarhiimpactfoundation.org/admin
- MongoDB: cloud.mongodb.com
- CIN: U88900AS2025NPL028634 · Section 8 Licence No. 171467
