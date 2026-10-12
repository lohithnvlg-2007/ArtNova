# ArtNova Security Policy

## Supported Versions

Security updates will be provided for the latest stable version of ArtNova.

| Version        | Supported |
| -------------- | --------- |
| Latest         | Yes       |
| Older versions | No        |

## Reporting a Vulnerability

If you discover a security vulnerability in ArtNova, please report it privately to the project administrator.

Do not publish sensitive information, passwords, API keys, personal data, or database credentials in public issues.

## Security Practices

* Supabase will be used for cloud data storage and authentication.
* Raspberry Pi will maintain a local backup of artwork records.
* Access to administrative features will be restricted to the administrator.
* Sensitive artist and owner information must be protected.
* Secrets and private credentials must never be committed to the GitHub repository.
* Data synchronization and deletion between Supabase and Raspberry Pi must be tested before production use.
