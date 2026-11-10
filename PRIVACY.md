# Privacy and Personal Data

This document describes how GRUB2 Theme Nuci at https://github.com/hmlendea/grub2-theme-nuci manages personal data. It covers the theme package and its distribution. The theme is a static asset collection with no runtime application logic.

## 📑 Table of Contents

- [What This Document Covers](#-what-this-document-covers)
- [Self-Hosted Deployments](#-self-hosted-deployments)
- [Data We Handle](#-data-we-handle)
  - [Data Provided to the Application](#data-provided-to-the-application)
  - [Data Generated or Collected by the Application](#data-generated-or-collected-by-the-application)
  - [Data Received from Integrations](#data-received-from-integrations)
- [Processing and Use](#-processing-and-use)
- [Storage, Retention, and Deletion](#-storage-retention-and-deletion)
- [External Processing and Integrations](#-external-processing-and-integrations)
- [Document Changes](#-document-changes)
- [Contact](#-contact)

## 🔎 What This Document Covers

This document describes how GRUB2 Theme Nuci at https://github.com/hmlendea/grub2-theme-nuci manages personal data. It covers the theme package and its distribution. The theme is a static asset collection with no runtime application logic. Where the software is self-hosted, the instance operator may have separate responsibilities described below.

## 🏠 Self-Hosted Deployments

This project is a GRUB2 theme package consisting of a theme definition file and icon assets. It is not a deployable application or service. Users copy the theme files to their local GRUB themes directory. The project maintainers do not operate any instance, control any deployment, or receive any data from theme usage. Instance operators (end users) control their local GRUB configuration, theme files, and boot process entirely.

No data is sent from a user's system to project maintainers or external services by the theme itself. The theme contains no telemetry, update checks, crash reports, authentication, reverse-proxy, object-storage, or monitoring integrations.

## 📥 Data We Handle

### Data Provided to the Application

No personal data is requested, collected, or processed by the theme package. The theme consists solely of a theme definition file (`Nuci/theme.txt`) and icon assets (`Nuci/icons/`).

### Data Generated or Collected by the Application

No personal data is generated or collected automatically by the theme package. The theme is a static asset collection with no runtime execution, logging, telemetry, or data generation capabilities.

### Data Received from Integrations

No personal data is received from integrations or third parties. The theme package has no integrations, network connectivity, or external data sources.

## 🧭 Processing and Use

The theme package does not process personal data. It provides visual styling for the GRUB bootloader through static configuration and image assets. No application function or processing activity involves personal data.

## 🗄️ Storage, Retention, and Deletion

The theme package stores no personal data. Theme files reside in the local filesystem at the GRUB themes directory (typically `/usr/share/grub/themes/Nuci/`). Users control installation, modification, and removal of theme files through standard filesystem operations. No backups, caches, or databases are created by the theme.

## 🔗 External Processing and Integrations

The theme package has no built-in external data transfer, integrations, or third-party services. It does not communicate with any external endpoints.

| Service or integration | Purpose | Data involved | Configuration or documentation |
|-----------------------|---------|---------------|--------------------------------|
| None | N/A | N/A | N/A |

## 🔄 Document Changes

Update this document when application data processes, storage, integrations, or deployment responsibilities change. The current version is published at https://github.com/hmlendea/grub2-theme-nuci/blob/master/PRIVACY.md.

## 📬 Contact

For questions about application data handling, contact the project maintainers via GitHub issues at https://github.com/hmlendea/grub2-theme-nuci/issues. Include the theme version or commit reference if applicable; do not send passwords, access tokens, or other secrets.