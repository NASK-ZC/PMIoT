# PMIoT – Pentesting Methodology of IoT

This repository contains supporting materials for the article PMIoT - Pentesting Methodology of IoT.
The article describes the overall testing logic and cross-layer approach, while this repository provides practical documents, procedures, and templates that can be used during IoT penetration testing engagements.

---

## Repository Content

### 1. Preparation Phase

**[Preparation Phase for IoT Penetration Testing](Preparation_phase_file.md)**

This document defines the preparation activities required before starting penetration testing of an IoT device. It describes the testing environment, required equipment, and software setup across all relevant layers.

Start from here to prepare the testing environment before following the layer-specific procedures.

---

### 2. Layer-Specific Pentesting Procedures

The following documents describe practical, step-by-step testing workflows for individual IoT system layers.

#### **[IoT Radio Layer Pentesting Procedure](IoT_Radio_Layer_Pentesting_Procedure.md)**

#### **[IoT IP Network Layer Pentesting Procedure](IoT_IP_Network_Layer_Pentesting_Procedure.md)**

#### **[IoT Web Application Layer Pentesting Procedure](IoT_Web_Application_Layer_Pentesting_Procedure.md)**

#### **[IoT Mobile Application Layer Pentesting Procedure](IoT_Mobile_Layer_Pentesting_Procedure.md)**

#### **[IoT Cloud Layer Pentesting Procedure](IoT_Cloud_Layer_Pentesting_Procedure.md)**

---

### 3. Procedure Form and Reporting Template

**[PMIoT Penetration Testing Procedure Form](PMIoT_pentest_procedure_form.docx)**

This document provides a short procedure form and reporting template for conducting and documenting IoT penetration tests.

It includes:

- Basic information about the tested device
- Application and manufacturer details
- A **Penetration Testing Procedure Checklist** guiding the tester through the testing process
- A **Penetration Testing Results Summary** for collecting and presenting key findings

You can use this document as a starting point for applying the methodology and recording test results in a structured format.

### 4. Vulnerability Details

**[Vulnerability Details](/Vulnerabilities_Details/)**

This directory provides detailed documentation for described in article vulnerabilities **[CVE-2023-3612](/Vulnerabilities_Details/CVE-2023-3612.md)**, **[CVE-2023-4617](/Vulnerabilities_Details/CVE-2023-4617.md)** and **[CVE-2025-10910](/Vulnerabilities_Details/CVE-2025-10910.md)**.

The documents include practical and explanatory materials, such as full vulnerability discovery stories, vulnerability descriptions, proof-of-concept (PoC) examples, insights, remediation considerations, comments and references to the relevant layers and testing procedures introduced in the paper.

## Suggested Reading Order

1. Read the article to understand the overall IoT security testing methodology and testing logic.
2. Read the **[Vulnerability Details](/Vulnerabilities_Details/)** to see practical examples of identified vulnerabilities, PoCs, remediation considerations, and how the proposed methodology and layer-specific procedures were applied in real testing scenarios.
3. Prepare the environment using **[Preparation Phase for IoT Penetration Testing](Preparation_phase_file.md)**.
4. Follow the relevant layer-specific guide depending on the scope of the test, you can use **[PMIoT Penetration Testing Procedure Form](PMIoT_pentest_procedure_form.docx)** to track performed tests and summarize results.
