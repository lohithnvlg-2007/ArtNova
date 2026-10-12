# ArtNova — Digital Artwork Registry & Authenticity Verification

ArtNova is a digital artwork registration and authenticity verification system designed to register artworks, maintain artist and ownership records, compare submitted artwork images against registered records, and provide secure access to authenticity information.

The system combines a web platform, Supabase cloud services, and a Raspberry Pi device that maintains local artwork data and synchronizes records with the cloud.

## Project Objectives

* Register artworks with unique NOVA IDs.
* Store artwork images, metadata, artist information, and ownership records.
* Compare submitted artwork images against registered artwork data.
* Generate artwork reports and certificates.
* Provide QR-based access to artwork-specific authentication pages.
* Allow the administrator to manage artwork records through a private dashboard.
* Provide separate complaint and service-request facilities.
* Maintain a local data backup on the Raspberry Pi SD card.
* Synchronize cloud and local data when internet connectivity is available.

## System Architecture

```text
                 ADMINISTRATOR
                       |
                       v
               Admin Web Dashboard
                       |
                       v
                  SUPABASE
           Database / Storage / Auth
                       |
             Internet Synchronization
                       |
                       v
                 RASPBERRY PI
                       |
                       v
                 Local SD Card
                       |
              Existing ArtNova Core
          Registration / Analysis /
             Verification / IDs
```

GitHub stores the source code and version history. The website is developed and tested on a laptop. Supabase provides cloud storage and backend services. The Raspberry Pi continues to run the existing ArtNova software and handles lightweight data synchronization and local storage.

## Main Components

### 1. Admin Dashboard

The administrator has access to the main management interface.

Planned features:

* View registered artwork images and their associated data.
* Search and open artwork records using NOVA IDs.
* Add, update, and manage artist information.
* Manage current and historical ownership records.
* View generated and verified artwork records.
* Review verification comparison results.
* Generate and download artwork reports.
* Review complaints and service requests.
* Respond to requests and close completed cases.
* Manage records and authorized deletion operations.

The administrator dashboard is private and must enforce authentication and authorization.

### 2. Artwork Authentication Website

Each artwork report will contain a QR code and a link to its specific authentication page.

Visitors will be able to:

* Open the artwork-specific page by scanning the QR code.
* Enter the required password.
* View permitted artwork and authenticity information.
* View available certificate details.
* Review recent verification records associated with the artwork.

Sensitive personal information must not be exposed to unauthorized visitors.

### 3. Complaint and Request Website

A separate website will allow users to submit complaints and service requests.

Users can choose between:

**Raise a Complaint**

* Certificate-related problems.
* Missing certificate.
* Website issues.
* Other artwork-related problems.

**Raise a Request**

* Request artwork-data deletion.
* Request an artwork-photo update.
* Request another supported change.

The process will require the NOVA ID and the user's phone number. The system will verify the phone number against the relevant stored record and require OTP verification before accepting the request.

Submitted complaints and requests will appear in the administrator dashboard. The administrator can review each case, write a response, update its status, and close it.

Requests involving deletion or changes to sensitive records must be checked and authorized before execution.

### 4. Supabase Backend

Supabase will serve as the cloud backend.

Planned responsibilities:

* PostgreSQL database for structured records.
* Storage for artwork images, artist photographs, reports, and certificates.
* Authentication for administrator and authorized users.
* Row Level Security (RLS) to restrict database access.
* Secure backend operations for sensitive changes.
* Records of ownership changes and verification activity.
* Synchronization metadata for the Raspberry Pi.
* Complaint and request management.

Public frontend code must never contain a Supabase service-role key or other privileged credentials.

### 5. Raspberry Pi Integration

The existing Raspberry Pi ArtNova software will remain the foundation of the device-side system.

Existing components include:

* `analyzer.py`
* `artnova_gui.py`
* `generate.py`
* Existing artwork data and image directories.

The precise responsibilities of each existing file will be documented during implementation.

New lightweight synchronization functionality will be added around the existing software rather than replacing it.

Planned capabilities:

* Upload newly registered artwork records to Supabase.
* Download authorized record updates from Supabase.
* Maintain local copies of relevant artwork and artist data.
* Queue pending uploads when the internet is unavailable.
* Resume synchronization after connectivity returns.
* Track synchronization status and errors.
* Process authorized cloud deletion events.
* Verify that local and cloud records agree.

The Raspberry Pi is not intended to host the public websites or run the cloud database.

### 6. Local Backup and Recovery

Important records will be maintained on the Raspberry Pi SD card.

The local data may include:

* Artwork metadata and NOVA IDs.
* Artwork images.
* Artist and ownership records.
* Verification records.
* Reports and certificates.
* Synchronization queues and status information.

The backup system will be designed to recover from temporary internet outages and support restoration after failures.

Local deletion must be coordinated with cloud deletion events. Critical data should be backed up and deletion operations verified before permanent removal. The SD card is an additional backup, not a substitute for an independent backup.

## Planned Repository Structure

```text
artnova/
├── README.md
├── CONTRIBUTING.md
├── SECURITY.md
├── .gitignore
│
├── admin-web/
│   └── Administrator dashboard
│
├── authentication-web/
│   └── Artwork-specific authentication pages
│
├── support-web/
│   └── Complaints and requests
│
├── supabase/
│   ├── migrations/
│   ├── functions/
│   └── config.toml
│
├── pi-agent/
│   ├── sync_agent.py
│   ├── sync/
│   ├── storage/
│   ├── security/
│   └── tests/
│
├── docs/
│   ├── architecture.md
│   ├── database.md
│   ├── synchronization.md
│   └── recovery.md
│
└── tests/
    ├── integration/
    └── security/
```

This is the proposed structure. Existing Raspberry Pi files and the eventual deployment layout will be preserved or adapted after the current repository and software have been reviewed.

## Technology Stack

| Component                  | Technology                                     |
| -------------------------- | ---------------------------------------------- |
| Source control             | Git and GitHub                                 |
| Web frontend               | HTML, CSS, JavaScript; framework to be decided |
| Cloud database             | Supabase PostgreSQL                            |
| Image and document storage | Supabase Storage                               |
| Authentication             | Supabase Auth and verified OTP workflows       |
| Raspberry Pi software      | Python                                         |
| Local storage              | SD card and local database/files               |
| Data exchange              | Secure HTTPS APIs                              |
| Testing                    | Automated and manual integration tests         |

## Data Synchronization Strategy

The intended data flow is:

1. The existing ArtNova software creates or updates an artwork record.
2. The Pi saves the record and its files locally.
3. The synchronization agent uploads pending changes to Supabase.
4. Changes made through authorized website workflows are recorded in the cloud.
5. The Pi downloads and applies those changes when online.
6. Synchronization status is checked and recorded.
7. Failed operations remain queued for retry.

Updates, conflicts, and deletions will use explicit synchronization rules to prevent accidental overwriting or loss of data. Cloud deletion events will be retained long enough for the Pi to process them reliably.

## Security Principles

* Keep privileged API keys and credentials out of GitHub.
* Use environment variables or protected configuration files for secrets.
* Enforce database authorization with Supabase RLS.
* Verify administrator permissions on the backend.
* Protect artist, owner, and contact information.
* Use OTP verification for supported sensitive workflows.
* Rate-limit authentication and OTP operations.
* Record important ownership changes and administrative actions.
* Validate uploaded files and user-provided input.
* Test backup restoration and synchronization before production deployment.

## Development Roadmap

* [ ] Review the existing Raspberry Pi ArtNova software.
* [ ] Finalize the GitHub repository structure.
* [ ] Design the Supabase database schema and access policies.
* [ ] Implement and test the administrator dashboard.
* [ ] Implement artwork-specific authentication pages.
* [ ] Implement the complaint and request portal.
* [ ] Implement report and certificate generation.
* [ ] Add the Raspberry Pi synchronization agent.
* [ ] Implement reliable local backup and recovery.
* [ ] Test upload, update, conflict, and deletion workflows.
* [ ] Perform security and end-to-end integration testing.
* [ ] Deploy the websites and enable production synchronization.

## Development Status

ArtNova is under active development. The existing Raspberry Pi software and data are the starting point for the new platform.

Features described as planned are not considered production-ready until they have been implemented and tested.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for development and contribution guidelines.

## Security

See [SECURITY.md](SECURITY.md) for security practices and vulnerability-reporting guidance.

## License

A project license has not yet been selected. All rights are reserved unless a license is added to this repository.

## Project Vision

ArtNova aims to provide a reliable and secure system for artwork registration, verification, record management, and authenticity information, combining cloud availability with local data resilience on the Raspberry Pi.
