# AI-Assisted Cybersecurity Policy Alignment Analyser

**Topic Approval and Project Scope Validation — Team Project Proposal — 09 Sep 2026**

## Scope

A procedural, RAG-enabled application that evaluates organisational cybersecurity policies against a selected security framework. The MVP will support policies and one public framework, initially NIST Cybersecurity Framework 2.0.

## 1. Problem Statement and Target Users

Organisations often maintain policies created by different teams and at different times. Manually mapping them to security-framework requirements is slow and inconsistent because terminology varies, evidence is scattered, and newer documents may conflict with older versions. The application will retrieve relevant policy passages and apply transparent rules to produce a traceable policy-alignment report. Intended users are compliance officers, internal auditors, risk analysts, SME security managers, and policy owners. The system assesses documented policy alignment only; it does not certify legal compliance or prove that controls operate in practice. Missing evidence is reported as insufficient evidence, not automatic non-compliance.

## 2. User Inputs

- One or more cybersecurity policy documents in PDF, DOCX, XLSX or TXT format.
- Document metadata, including policy title, version, approval status, policy owner, effective date, review date, organisational scope and business unit
- A supported framework/version that's preloaded into the application
- Assessment context: relevant business unit/system and the scope of the assessment

The terminal interface validates paths, file types, dates, required values, and framework choices; invalid input is rejected and requested again.

## 3. Use of AI

Every control-assessment record passes through the AI Manager. Uploaded policies are indexed using RAG. For each control, relevant passages and the control requirement are sent to the AI API. AI performs semantic comparison where fixed keyword matching would fail because equivalent policy concepts may use different wording.

- Classify evidence as supports, partially supports, contradicts, or unrelated.
- Return exact quotations with document and page/section references.
- Identify missing policy elements, scope mismatches, contradictions, and extracted governance metadata.
- Return confidence only to identify uncertain interpretations requiring manual review.
- Recommend fixes based on the issues discovered.

Responses must follow a defined JSON schema. The AI Manager validates required fields, types, allowed values, and citations; malformed output is retried and API failure is logged without crashing. AI supplies structured evidence but does not make the final policy-alignment decision.

## 4. Business Rules

- **Applicability:** assess only controls relevant to the selected scope; uncertain applicability requires manual review.
- **Evidence acceptance:** count evidence only when its quotation is verified, its scope matches, and the policy is current and approved/published. Exclude draft, expired, superseded, or unrelated evidence.
- **Requirement evaluation:** apply JSON rules such as all_of, any_of, mandatory elements, blocking elements, and control dependencies. A blocking failure cannot be cancelled by successful optional elements.
- **Conflict handling:** opposing statements in current approved policies produce a conflicting-policies result and manual review.
- **Final outcomes:** documented, partially documented, insufficient evidence, outdated evidence, material policy gap, conflicting policies, or manual review.
- **Priority:** use control criticality, blocking failures, contradictions, and previous assessment history. Repeated gaps will be highlighted and have a higher priority.

**Multi-condition rule:** if a control is high/critical, a mandatory blocking element is missing or contradicted, and the gap appeared previously, classify it as an urgent recurring policy gap. Low-confidence interpretation will flag the finding for manual review.

## 5. Data Manager

The Data Manager provides persistent storage for all processed assessment records in a structured JSON file. On startup it loads previous assessments, creating a new store if none exists and backing up and resetting the file if it is missing or corrupt, without crashing. After each assessment it atomically saves the assessment metadata, scope, policy information and final control results in a sorted format. It exposes filter and query functions by framework, control, status, priority, scope and business unit to support the dashboard search interface. It also supplies control history lookups so the Logic Manager can prioritise urgent findings and flag recurring gaps from past reports.

## 6. Github Repository

This is the link to the repository that's storing the code of our project: https://github.com/Manas-2601921/Group9Repo.git