---
title: What's new in the MSIXMGR tool - Azure Virtual Desktop
description: Learn about what's new in the release notes for the MSIXMGR tool.
ms.topic: release-notes
author: dougeby
ms.author: avdcontent
ms.date: 06/04/2025
ms.custom: docs_inherited
---

# What's new in the MSIXMGR tool

This article provides the release notes for the latest updates to the MSIXMGR tool, which you use for expanding MSIX-packaged applications into MSIX images for use with [App Attach in Azure Virtual Desktop](app-attach-overview.md).

## Latest available version

The following table shows the latest available version of the MSIXMGR tool.

| Release | Latest version | Download |
|---------|----------------|----------|
| Public | 1.2.0.0 | [MSIXMGR tool](https://aka.ms/msixmgr) |

## Version 1.2.0.0

*Published: April 18, 2023*

In this release, we've made the following changes:

- MSIXMGR now supports the expansion of MSIX bundles.
- Support for creating a VHD image without the size parameter.
- Improved support for creation of MSIX images as *CIM* files without the need to provide the VHD size parameter.
- Support for apps with long package paths (over 128 characters).

## Next steps

To learn more about the MSIXMGR tool, check out these articles:

- [Using the MSIXMGR tool](app-attach-msixmgr.md).
- [MSIXMGR tool parameters](msixmgr-tool-syntax-description.md)

