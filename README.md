# ArtNova — Digital Artwork Registry & Authenticity Verification

**ArtNova** is a digital artwork registration, analysis, authenticity verification, and record-management platform. It combines a Raspberry Pi-based artwork processing system with a cloud-connected web platform, secure artwork authentication, administrative management, and a dedicated complaint and service-request system.

The project aims to create a reliable record of registered artworks, preserve artist and ownership information, compare submitted artwork images against registered data, generate reports and certificates, and maintain local backups alongside cloud storage.

**Project status:** Under development. Existing Raspberry Pi functionality is the foundation of the new platform. Features described as planned are not considered operational until implemented and tested.

---
...................................................................................................................................................
## 1. Project Objectives

* Assign a unique NOVA ID to every registered artwork.
* Store artwork images, registration details, and associated metadata.
* Analyze submitted artwork images and compare them with registered records.
* Display comparison values and relevant candidate matches.
* Generate artwork reports and certificates with QR codes.
* Maintain artist information and ownership history.
* Provide a private administrator dashboard.
* Provide a separate website for artwork authentication.
* Provide a separate website for complaints and service requests.
* Store records and files in Supabase.
* Maintain local artwork data and backups on the Raspberry Pi SD card.
* Synchronize authorized changes between the Raspberry Pi and Supabase.
* Support operation during temporary internet outages.

## 2. System Architecture

```text
                      ADMINISTRATOR
                            |
                            v
                  ADMIN WEB DASHBOARD
                            |
                            v
                      SUPABASE
              Database / Storage / Auth
                    /             \
                   v               v
          AUTHENTICATION WEB    SUPPORT WEB
           QR-based access     Complaints/Requests
                   |
                   |
             Secure HTTPS APIs
                   |
                   v
                RASPBERRY PI
                   |
                   v
            EXISTING ARTNOVA CORE
         Registration / Analysis /
             Verification / IDs
                   |
                   v
                 SD CARD
          Local Data and Backup
```

### Architecture responsibilities

| Component              | Purpose                                                              |
| ---------------------- | -------------------------------------------------------------------- |
| GitHub                 | Source code, documentation, and version control                      |
| Development laptop     | Build and test the websites and backend integration                  |
| Admin website          | Manage artwork, artists, owners, reports, and support cases          |
| Authentication website | Display authorized information for a particular artwork              |
| Support website        | Accept complaints and service requests                               |
| Supabase               | Cloud database, file storage, authentication, and backend operations |
| Raspberry Pi           | Existing artwork processing, local persistence, and synchronization  |
| SD card                | Local records, files, and pending synchronization operations         |

The websites will be hosted independently of the Raspberry Pi. The Pi will remain a lightweight device and will not host the public websites or run the cloud database.

## 3. Main System Components

### 3.1 Existing Raspberry Pi ArtNova Software

The current Raspberry Pi application is the starting point for this project. Its existing registration, analysis, and verification functionality should be preserved while the new features are integrated.

Existing files include:

* `analyzer.py`
* `artnova_gui.py`
* `generate.py`

Their exact responsibilities and dependencies will be documented after inspecting the current source code.

Existing artwork images, JSON records, NOVA IDs, verification records, and configuration must be backed up before modifications are made.

### 3.2 Administrator Dashboard

The administrator dashboard is the private management interface for the project owner.

Planned capabilities:

* View registered artwork images and associated records.
* Search and filter artwork using NOVA IDs.
* Open complete artwork details.
* Add and update artist information.
* Manage current and previous ownership records.
* View generated artwork and verification results.
* View comparison images and comparison values.
* Generate and download PDF reports.
* Manage certificates and permitted document updates.
* View recent verification records.
* Review complaints and service requests.
* Write responses and close completed cases.
* Perform authorized updates and deletion operations.

All privileged actions must be authenticated and authorized by the backend, not merely hidden in the frontend.

### 3.3 Artwork Authentication Website

Each artwork report will contain a QR code linking to the corresponding artwork-specific authentication webpage.

The planned user journey is:

1. Scan the QR code.
2. Open the artwork-specific URL.
3. Enter the required password.
4. Authenticate successfully.
5. View the permitted artwork details and authenticity information.
6. Review the available certificate and barcode.
7. View recent verification records associated with that artwork.

Each user-facing page must be restricted to the intended artwork record. Passwords must be securely stored, and private artist or owner information must not be exposed without authorization.

### 3.4 Complaints and Service-Request Website

A separate website will allow users to raise complaints or request changes to their artwork records.

The landing page will provide two choices.

#### Option A: Raise a Complaint

Planned complaint categories:

* Certificate missing.
* Certificate-related issue.
* Website issue.
* Other artwork-related complaint.

#### Option B: Raise a Request

Planned request categories:

* Request artwork-data deletion.
* Request an artwork-photo update.
* Request another supported record change.

#### Verification and processing workflow

1. The user chooses a complaint or request.
2. The user enters the NOVA ID and phone number.
3. The backend checks whether the phone number matches the relevant stored record.
4. An OTP is sent to the verified phone number.
5. The backend validates the OTP.
6. The complaint or request is saved in Supabase.
7. The submission appears in the administrator dashboard.
8. The administrator reviews the case.
9. The administrator writes a response and performs any approved action.
10. The case is marked as resolved or closed.

OTP verification should be performed by a trusted backend service. Phone numbers and OTPs must not be trusted merely because they were submitted by the browser.

Deletion requests and changes to sensitive records must undergo appropriate authorization checks before execution.

## 4. Artwork Reports and Certificates

The platform is intended to generate downloadable reports and artwork certificates.

Depending on the document type, the generated document may contain:

* Artwork image.
* Unique NOVA ID.
* Registration date and time.
* Available location information.
* Artist information.
* Current owner information, where authorized.
* Verification results and comparison values.
* Certificate reference.
* Secure QR code.
* Barcode.
* Signature or verification code.

Reports may also include comparison images and recent verification information.

Generated documents must distinguish between artwork registration and verified findings. A certificate, QR code, barcode, or signature code alone does not establish that an artwork is authentic.

## 5. Artwork Analysis and Verification

The existing ArtNova analysis process will be retained as the foundation of artwork comparison.

The planned verification workflow is:

1. Receive an image submitted for verification.
2. Analyze the image using the existing ArtNova processing pipeline.
3. Compare it against relevant registered artwork records.
4. Calculate comparison values supported by the implemented analysis method.
5. Display relevant matching candidates and their comparison results.
6. If no reliable match is found, indicate that the result is inconclusive and provide appropriate next steps.

Possible outcomes include:

* A registered artwork candidate is identified.
* A possible match is found but requires further review.
* No relevant registered record is found.
* The image is too damaged or unclear for a reliable comparison.
* Expert assistance is recommended.

A low similarity score or missing database record must not automatically be treated as proof of forgery. Results must reflect the limitations of the actual verification algorithm.

## 6. Supabase Backend

Supabase will provide the cloud data and backend services.

Planned responsibilities:

* PostgreSQL database for structured records.
* Storage for artwork images, artist photographs, reports, and certificates.
* Authentication for the administrator and authorized users.
* Row Level Security (RLS) for database access restrictions.
* Trusted backend operations for sensitive changes.
* Artwork and ownership history.
* Verification records.
* Complaint and request management.
* Synchronization state and change tracking.

The final database schema will be designed before production integration.

### Proposed data entities

| Entity                 | Purpose                                                      |
| ---------------------- | ------------------------------------------------------------ |
| `artworks`             | Artwork identity, NOVA ID, metadata, and registration status |
| `artists`              | Artist profile and associated information                    |
| `ownership_history`    | Current and historical ownership records                     |
| `verification_records` | Comparison results and verification history                  |
| `certificates`         | Certificate metadata and document references                 |
| `reports`              | Generated report metadata and file references                |
| `complaints`           | Complaint details, responses, and status                     |
| `service_requests`     | Requested changes, verification, and resolution status       |
| `sync_events`          | Changes that must be synchronized with the Pi                |
| `audit_logs`           | Important administrative and security-related actions        |

These are proposed entity names, not a claim that the tables already exist. The final schema may differ after the current application and data format have been reviewed.

## 7. Raspberry Pi Integration

The Raspberry Pi will remain responsible for the existing ArtNova processing functionality and local data storage.

Its planned responsibilities are:

* Run the existing artwork registration and analysis software.
* Save newly created records and files locally.
* Upload pending records to Supabase when online.
* Download authorized cloud changes.
* Maintain local copies of relevant artwork and artist data.
* Queue pending operations during internet outages.
* Resume synchronization when connectivity returns.
* Apply authorized updates and deletion events.
* Track synchronization errors and status.
* Support restoration from local backup.

The Pi has limited memory and processing resources. New synchronization functionality should therefore be lightweight, avoid unnecessary background workloads, and reuse the existing application wherever practical.

The Pi is not intended to run the administrator dashboard, public authentication website, support website, or cloud database.

## 8. Data Synchronization

The intended data flow is:

```text
Artwork Created or Updated on Pi
               |
               v
       Save Data Locally
               |
               v
       Queue Sync Operation
               |
               v
       Is Internet Available?
            /       \
          No         Yes
          |           |
    Keep Operation   Connect to Supabase
    in Local Queue         |
                           v
                  Upload Pending Changes
                           |
                           v
                  Download Cloud Changes
                           |
                           v
                  Apply Authorized Changes
                           |
                           v
                  Verify Sync Status
                           |
                           v
                  Record Completed Sync
```

The synchronization system must handle:

* Duplicate uploads.
* Interrupted file transfers.
* Conflicting updates.
* Missing or invalid files.
* Failed downloads.
* Offline changes.
* Cloud deletion events.
* Recovery after unexpected power loss.

### Synchronization rules

* Every record should have a stable identifier.
* Retried operations must not create duplicate records.
* The system must track which changes have already been processed.
* Conflicting updates must follow explicit resolution rules.
* Failed operations must remain queued for retry.
* Deletion events must remain available until the Pi has processed them.
* Sync status must be recorded so failures can be diagnosed.

The implementation must not rely only on timestamps or blindly overwrite newer records.

## 9. Local Backup and Recovery

The Raspberry Pi SD card will retain local copies of important records and files.

The local backup may include:

* Artwork metadata and JSON records.
* Artwork images.
* Artist records.
* Ownership history.
* Verification records.
* Reports and certificates.
* Synchronization queues and state.

The backup system should support operation during temporary internet outages and provide a recovery path after failures.

### Deletion synchronization

If a record is deleted through an authorized website operation, the cloud backend must record the deletion event so that the Pi can process it after reconnecting.

The system should:

1. Authenticate and authorize the deletion.
2. Record the requested operation.
3. Preserve required audit information.
4. Propagate the deletion to the Pi.
5. Confirm that the local operation was processed.
6. Update synchronization status.

Deletion operations must be idempotent, so retrying the same operation does not cause unintended effects.

Permanent deletion should be enabled only after backup, authorization, and synchronization behaviour have been tested. The retention policy must also be defined.

The SD card provides an additional backup layer, not a replacement for independent backups. Important records should also have a separate recovery copy.

## 10. Security Principles

Security is a core requirement because ArtNova handles artwork records and potentially sensitive personal information.

The implementation must:

* Keep privileged API keys out of frontend code and GitHub.
* Store secrets in protected environment configuration.
* Enforce backend authorization for administrative actions.
* Apply suitable Supabase RLS policies.
* Restrict access to private files.
* Protect artist, owner, email, and phone information.
* Validate OTPs on the backend.
* Rate-limit login and OTP operations.
* Validate uploaded files and user-provided input.
* Use secure password storage and authentication workflows.
* Record important administrative changes.
* Restrict destructive operations to authorized users.
* Test recovery and synchronization before production deployment.

The Supabase publishable key may be used in a public frontend when the database and storage policies are correctly configured. A service-role key or equivalent privileged secret must never be exposed in public frontend code.

Real credentials, OTPs, private documents, and production personal data must not be committed to GitHub.

## 11. Proposed Repository Structure

The following structure describes the intended future repository. It is a plan for development, not a statement that every file already exists.

```text
artnova/
│
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── SECURITY.md
├── .gitignore
│
├── admin-web/
│   ├── index.html
│   ├── assets/
│   └── src/
│
├── authentication-web/
│   ├── index.html
│   ├── assets/
│   └── src/
│
├── support-web/
│   ├── index.html
│   ├── assets/
│   └── src/
│
├── supabase/
│   ├── migrations/
│   ├── functions/
│   └── config.toml
│
├── pi-agent/
│   ├── sync_agent.py
│   ├── sync/
│   │   ├── uploader.py
│   │   ├── downloader.py
│   │   ├── queue.py
│   │   ├── delete_handler.py
│   │   └── sync_state.py
│   ├── storage/
│   │   ├── local_store.py
│   │   └── file_manager.py
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

The actual layout will be adapted to the existing repository and the chosen deployment method. Existing Raspberry Pi files should not be moved or replaced until their dependencies have been checked.

## 12. Technology Stack

| Component             | Technology                                          |
| --------------------- | --------------------------------------------------- |
| Version control       | Git and GitHub                                      |
| Web frontend          | HTML, CSS, JavaScript; framework to be finalized    |
| Cloud database        | Supabase PostgreSQL                                 |
| File storage          | Supabase Storage                                    |
| Authentication        | Supabase Auth and verified OTP workflows            |
| Backend operations    | Secure server-side functions                        |
| Raspberry Pi software | Python                                              |
| Local storage         | SD card, local files, and a suitable local database |
| Network communication | HTTPS APIs                                          |
| Testing               | Automated tests and end-to-end integration tests    |

## 13. Development Setup

Development will take place primarily on a laptop. The Raspberry Pi will continue running its existing software until new functionality is ready for controlled testing.

### Prerequisites

* Git and GitHub access.
* Python 3 for Raspberry Pi development.
* A modern web browser.
* A Supabase project.
* The existing ArtNova source code and data.
* Protected local environment configuration.

### Development workflow

1. Clone the repository to the development machine.
2. Review and back up the existing Pi software and data.
3. Document the existing registration and analysis workflow.
4. Design the Supabase schema and security policies.
5. Develop the websites locally.
6. Implement and test trusted backend operations.
7. Build the Raspberry Pi synchronization agent separately.
8. Test online and offline operation.
9. Test updates, conflicts, and deletion propagation.
10. Test backup restoration.
11. Deploy the websites and enable production synchronization only after successful testing.

Exact installation commands will be documented after the final framework and repository structure are selected.

## 14. Development Roadmap

* [ ] Review the existing Raspberry Pi software and data structure.
* [ ] Finalize the GitHub repository structure.
* [ ] Design the Supabase database schema.
* [ ] Configure authentication and access policies.
* [ ] Build the administrator dashboard.
* [ ] Build the artwork authentication website.
* [ ] Build the complaint and service-request website.
* [ ] Implement reports and certificates.
* [ ] Develop the Raspberry Pi synchronization agent.
* [ ] Implement offline queues and change tracking.
* [ ] Implement safe cloud-to-Pi deletion propagation.
* [ ] Implement backup and recovery procedures.
* [ ] Test authentication, authorization, and data privacy.
* [ ] Perform end-to-end integration testing.
* [ ] Deploy the websites and enable production synchronization.

## 15. Contributing

Please see [CONTRIBUTING.md](CONTRIBUTING.md) for project development guidelines, testing expectations, and contribution procedures.

## 16. Security Reporting

Please see [SECURITY.md](SECURITY.md) for security practices and instructions for reporting vulnerabilities.

Do not publish credentials, private artist information, or sensitive production data in public issues.

## 17. License

ArtNova is licensed under the **Apache License, Version 2.0**.

You may use, modify, and distribute the software in accordance with the terms of this license.

See the [LICENSE](LICENSE) file for the complete license text.

## 18. Project Vision

ArtNova aims to create a reliable digital registry for artwork registration, comparison-based verification, artist and ownership records, and secure authenticity information.

By combining cloud availability with local Raspberry Pi storage, the project aims to maintain data availability, support recovery from connectivity problems, and provide a consistent system for artwork management.

The platform will be developed incrementally, preserving the existing Raspberry Pi functionality while introducing the new websites, cloud backend, and synchronization features.
