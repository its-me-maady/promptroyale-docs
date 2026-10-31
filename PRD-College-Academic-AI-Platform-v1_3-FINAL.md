# Product Requirements Document (PRD)

**Product:** College Academic AI Platform\
**Product name:** Prompt Royale\
**Version:** 1.3\
**Status:** Final baseline for this revision\
**Supersedes:** PRD v1.2-FINAL\
**Audience:** Product owner, college stakeholders, engineering, QA and
operations

> **Document status note:** This document is the approved product
> baseline for this revision. It retains explicitly unresolved legal,
> commercial and infrastructure decisions in the Open Decisions
> Register. "Final" does not mean those separately tracked decisions
> have been made or that the product is production-release ready.

------------------------------------------------------------------------

## 1. Product Overview

### 1.1 Description

The College Academic AI Platform is a college-specific AI academic
assistance platform. It allows students to ask academic questions and
receive answers grounded in approved academic documents uploaded by
their college.

The platform applies subject-based access control. A student can
retrieve information only from subjects to which the student currently
has access. Generated answers must include citations to the supporting
academic material. When available material is insufficient, the platform
must communicate that limitation rather than present unsupported content
as sourced academic information.

Each college receives a separate cloud-hosted environment. The product
consists of:

-   An Android application for students.
-   A web application for Department Users.
-   A web application for College Administrators.
-   An internal Vendor Support capability restricted to platform
    diagnostics.

### 1.2 Vision

Make a college's approved academic knowledge accessible through a
controlled AI interface, helping students understand and revise their
subjects using institution-provided learning materials.

### 1.3 Product Positioning and Boundaries

The platform is an AI-powered academic assistance product. It is not an
ERP, Learning Management System (LMS), Student Information System (SIS),
or general-purpose open-domain chatbot.

The platform does not provide attendance management, examination
scheduling, marks or grades management, fee collection, payroll,
human-resource management, timetable management, general
student-information management, course delivery, assignment submission,
or institutional messaging/social networking in version 1.

### 1.4 Product Principles

1.  Academic answers are grounded in documents provided by the relevant
    college.
2.  Access is controlled by current subject membership.
3.  Answers provide source citations.
4.  Each college's environment and data are isolated from other
    colleges.
5.  Student conversations are private from routine department,
    college-administrator and vendor-support access.
6.  AI usage follows the configured service arrangement.
7.  The platform communicates insufficient evidence rather than
    fabricating academic support.

------------------------------------------------------------------------

## 2. Goals and Success Measures

### 2.1 Product Goals

-   Provide students with an accessible AI interface for
    college-approved academic materials.
-   Reduce the effort required to locate and understand information
    across academic documents.
-   Ensure answers are relevant to subjects currently accessible to each
    student.
-   Make the source of generated academic answers transparent.
-   Allow departments to organize subjects, students and academic
    documents.
-   Give college administrators college-level administrative and billing
    oversight.
-   Establish a repeatable commercial product deployable for multiple
    colleges.

### 2.2 Success Measures

  -----------------------------------------------------------------------
  Area                                Measure / target
  ----------------------------------- -----------------------------------
  Answer quality                      At least 90% correctness on the
                                      agreed evaluation set

  Citation quality                    At least 95% valid citations on the
                                      agreed evaluation set

  Adoption                            Student activation and recurring
                                      usage

  Document ingestion                  Successful document-processing rate

  Unsupported answers                 Appropriate insufficient-evidence
                                      response rate

  Reliability                         Availability, failure rate and
                                      response latency

  Access control                      All defined authorization tests
                                      pass

  College isolation                   All cross-college isolation tests
                                      pass

  Feedback                            Feedback volume and recurring issue
                                      trends

  Commercial performance              College retention and deployment
                                      sustainability
  -----------------------------------------------------------------------

The answer-quality and citation targets apply independently to each
supported provider/model combination. Results for one provider must not
be assumed to apply to another.

------------------------------------------------------------------------

## 3. Users, Roles and Platforms

### 3.1 Student

**Platform:** Android application.

Students can: - Activate and sign in to their account. - View subjects
currently assigned to them. - Ask academic questions. - View answers and
source citations. - Access their own conversation history. - Delete
individual conversations or clear their history. - Configure, replace or
revoke their own BYOK provider credentials. - Submit feedback about an
answer. - Participate in Quiz Battle.

Students cannot manage academic documents, access unassigned subjects,
view other students' conversations, or access staff administration
functions.

### 3.2 Department User

**Platform:** Web application.

Department Users can: - Manage students within their authorized
department. - Create and manage subjects within that department. -
Assign and remove students from subjects. - Upload academic documents to
subjects. - View document-processing status. - Manage academic resources
within their authorized scope.

Department Users have no routine access to individual student
conversations or individual student usage.

### 3.3 College Administrator

**Platform:** Web application.

College Administrators can: - Create and manage departments. - Manage
college-level administrative configuration. - View aggregate platform
usage information. - Review billing information and invoices. - Perform
authorized college-level administrative operations.

College Administrators have no routine access to individual student
conversations or individual student usage.

### 3.4 Vendor Support

Vendor Support is an internal platform-support capability, not a normal
college tenant role. It is limited to authorized diagnostics and
platform operational information. Vendor Support has no routine access
to student conversation content.

### 3.5 External AI Providers

Version 1 provider integrations are: - OpenRouter - Google Gemini -
OpenAI - DeepSeek - Anthropic Claude

Each provider/model combination must independently pass quality and
citation evaluation before being offered. Provider-specific data
handling and disclosure requirements must be respected.

------------------------------------------------------------------------

## 4. Product Scope and Platforms

### 4.1 Included in Version 1

-   Separate cloud-hosted environment per college.
-   Student Android application.
-   Department User and College Administrator web applications.
-   College and department management.
-   Student account activation.
-   Subject creation and subject-membership management.
-   Academic document upload and processing.
-   Subject-scoped academic question answering.
-   Source citations.
-   Conversation history and student-controlled deletion.
-   Platform-Managed and Bring Your Own Key (BYOK) service arrangements.
-   Usage tracking and college billing.
-   Student feedback.
-   Solo Quiz Battle.
-   Vendor diagnostics and platform support.

### 4.2 Supported Platforms and Language

-   Student application: Android only.
-   Minimum supported Android version: Android 8.0 (API 26).
-   Department and College Administrator interfaces: web.
-   Launch language: English only.
-   iOS is excluded from version 1.

------------------------------------------------------------------------

## 5. Product Requirements

### 5.1 Authentication and Account Management

-   Protected features require authentication.
-   Student accounts are created and managed through the college's
    administrative workflow.
-   Students receive email activation links.
-   Activation requires the student to create a password.
-   Shared or default passwords are not used.
-   Account status and permissions are checked when protected features
    are accessed.
-   Suspended and deactivated accounts cannot use protected student
    features.

### 5.2 College, Department and Subject Management

-   Each college has a separate deployment environment.
-   A College Administrator creates and manages departments.
-   Department Users manage students, subjects and academic resources
    within their authorized department.
-   Subjects belong to departments.
-   Student access is granted through subject membership.
-   Access is based on subject membership, not semester or year.
-   The model supports arrear subjects and other cross-semester subject
    assignments.
-   Removing a student's membership prevents future retrieval from that
    subject.

### 5.3 Academic Document Management

Department Users upload academic materials to subjects.

**Supported formats:** PDF (including scanned PDF), DOCX, TXT, Markdown,
PNG and JPEG.

**Limits:** - Maximum file size: 25 MB. - Maximum PDF length: 200 pages.

Document processing begins automatically after a successful upload.
Per-document approval is not required.

Document processing states: - Pending - Processing - Available - Failed

A replacement document does not replace the prior available version for
retrieval until the replacement has processed successfully. Scanned and
handwritten material must carry an appropriate confidence indicator;
handwriting quality is evaluated separately.

### 5.4 Academic Question Answering

Students can ask academic questions about currently accessible subjects.

The platform: - Retrieves only from documents in subjects to which the
student currently has membership. - Re-checks subject access at
retrieval time. - Uses college-approved academic documents as its
knowledge source. - Provides citations to supporting documents. -
Communicates when the available material is insufficient. - Does not
present unsupported material as if it were grounded in college
documents.

Conversation history may be displayed in full. Only a bounded,
permission-filtered portion may be supplied as context for a new AI
request.

### 5.5 Conversation Management

-   Students can view their own conversation history.
-   Students can delete an individual conversation or clear all history.
-   Deleted conversations are excluded from the active application view
    immediately; permanent deletion follows the retention policy.
-   If subject membership is removed, existing conversation history
    remains governed by the retention policy, but citations/source
    previews to now-restricted documents display **"Source no longer
    available."**
-   Follow-up questions cannot retrieve material from subjects the
    student can no longer access.

### 5.6 Privacy and Data Retention

#### 5.6.1 Routine Conversation Access

The approved privacy decisions are:

  -----------------------------------------------------------------------
  Stakeholder                         Access
  ----------------------------------- -----------------------------------
  Department Users                    No routine access to individual
                                      student conversations

  College Administrators              Aggregate usage information only;
                                      no routine access to individual
                                      conversations

  Vendor Support                      Diagnostics only; no routine
                                      conversation-content access

  Feedback reviewers                  Only content explicitly submitted
                                      through feedback
  -----------------------------------------------------------------------

#### 5.6.2 Active Student Accounts

Conversation data for active accounts is retained without automatic
time-based deletion under the current product decision. This remains
subject to legal review, particularly regarding data minimization and
applicable Indian privacy requirements.

#### 5.6.3 Deactivated Student Accounts

Upon deactivation: - Student access is removed immediately. -
Conversation data is retained for two months and then deleted. - Backups
containing deleted data are purged six months after the original
deletion event. - BYOK credentials are deleted immediately.

#### 5.6.4 Student-Controlled Deletion

Students may delete individual conversations or clear all history.
Content is removed from the active application view immediately;
permanent deletion follows the applicable retention process.

#### 5.6.5 Privacy Decision 7 --- AI Training and Model Improvement

**Status: OPEN.**

The platform's own policy on using student conversations or uploaded
academic documents for AI model training or model improvement has not
been finalized. This is separate from the data-handling and training
policies of external AI providers.

Provider-specific disclosures and acknowledgments must be finalized
before provider credential setup is released. No assumption about
platform training or provider training is to be treated as an approved
product rule until the relevant policy is resolved.

### 5.7 Feedback

Students can submit feedback about an AI answer. Before submission, the
platform must disclose exactly what content will be shared with
reviewers. Reviewers can access only the specific submitted
message/answer pair and associated feedback, not the student's full
conversation history.

### 5.8 Usage Tracking

**Platform-Managed:** - The platform pays for AI inference. - Input and
output token usage is tracked per student. - Retrieved context included
in the AI request is included in usage accounting.

**BYOK:** - The student uses their own provider credential. - The
platform tracks provider-key health status. - BYOK token usage is not
treated as platform-paid inference usage.

### 5.9 AI Providers and Service Tiers

#### Platform-Managed

-   AI inference is paid for by the platform.
-   Proposed fee: ₹100 per active student per month.
-   The platform manages provider configuration and usage accounting.

#### Bring Your Own Key (BYOK)

-   Students configure their own supported provider credential.
-   The platform does not pay for the student's inference usage.
-   Proposed platform fee: ₹40 per active student per month.
-   The BYOK fee remains subject to confirmation.

#### Provider Setup

Gemini is the recommended first provider shown to a student who has not
configured a credential. Its availability and applicable free-tier terms
must be reverified before launch and periodically thereafter.

Other supported provider choices remain available. Each provider
requires its own disclosure and acknowledgment before a credential is
saved. Final disclosure wording depends on Privacy Decision 7 and the
relevant provider terms.

### 5.10 Billing, Trial and Suspension

#### Pricing Structure

-   Fixed college infrastructure/support fee: ₹3,000--₹4,000 per college
    per month (range; final single value not yet selected).
-   Platform-Managed fee: ₹100 per active student per month.
-   BYOK fee: proposed ₹40 per active student per month.

#### Billing Rules

-   Billing follows calendar months.
-   Active-student snapshot is generated on the 28th of each month.
-   Students activated after the snapshot are billed from the next
    period.
-   Students deactivated after the snapshot remain billable for the
    current period.
-   No proration applies under the current policy.

#### Overdue Invoices

-   Grace period: three days after the due date.
-   If payment remains unpaid after the grace period, student access is
    suspended.
-   Suspension is distinct from deactivation.
-   Suspended students remain included in the billing snapshot unless
    separately deactivated.

#### Trial

-   Trial duration: 14 days.
-   Trial access is limited by subject and document caps.
-   On expiry without conversion, trial-created documents, conversations
    and configuration are deleted/reset.
-   Conversion to a paid plan starts fresh; trial data does not carry
    over in version 1.

### 5.11 Quiz Battle --- Solo Score/Time Attack

A student selects one currently accessible subject and starts a solo
timed battle.

Rules: - Exactly 10 multiple-choice questions per battle. - Questions
are generated exclusively from documents belonging to the selected
subject. - One difficulty level per battle; there are no difficulty
tiers. - Each question has a source citation. - If source material is
insufficient to generate 10 valid questions, the platform displays an
explicit "not enough material" message rather than fabricating
questions. - The student sees the score and time taken within the live
session. - Results are not persisted as conversation history, quiz
history or leaderboard data. - Department Users and College
Administrators cannot view individual battle results. - If subject
membership is revoked during a battle, the battle terminates
immediately. - Generation calls use the normal AI provider and usage
arrangement. - There is no separate Quiz Battle fee. - A student may
start a maximum of **3 battles per calendar day**, based on the
college's local time. - A fourth attempt is blocked with a clear
daily-limit message and reset time.

------------------------------------------------------------------------

## 6. Non-Functional Product Requirements

### 6.1 Security

-   Authentication is required for protected operations.
-   Authorization is enforced server-side.
-   Subject membership is checked at retrieval time.
-   Cross-college data access is prevented.
-   BYOK credentials are protected and never written to plaintext logs.
-   Privileged support actions are auditable.
-   The platform includes defenses against prompt injection and attempts
    to bypass subject-level access.
-   Sensitive data is not unnecessarily included in diagnostic logs.

Staff MFA is not required at launch. This is an accepted risk and a
post-launch hardening candidate; the design should allow future MFA.

### 6.2 Capacity

Planning target per college deployment: - 150 student accounts. - Up to
100 concurrent users.

Load testing is required before production commitments.

### 6.3 Reliability and Recovery

-   Recovery Time Objective (RTO): maximum 4 hours.
-   Tier 1 data: documents, subjects, accounts and billing records;
    near-zero Recovery Point Objective (RPO).
-   Tier 2 conversation data may tolerate loss within the agreed
    recovery window.
-   Battle usage counters are low-value enforcement data and may use a
    less stringent recovery policy.

Specific backup technology and frequency remain technical-design
decisions.

### 6.4 Hosting and Isolation

Each college receives a separate cloud-hosted environment. The cloud
provider is not yet selected. Selection must consider isolation,
security, data residency, reliability, recovery, capacity and operating
cost.

### 6.5 AI Quality

-   At least 90% answer correctness on the agreed evaluation set.
-   At least 95% citation validity on the agreed evaluation set.
-   Each supported provider/model combination is evaluated
    independently.
-   Handwriting and scanned-document extraction are evaluated
    separately.

------------------------------------------------------------------------

## 7. Commercial Model

The current commercial model consists of: 1. A fixed college-level
infrastructure and support fee. 2. A per-active-student fee determined
by service tier. 3. Platform-Managed inference included in the Managed
student fee. 4. Student-owned inference credentials in BYOK, with a
separate platform fee.

The active-student count is determined by the monthly snapshot on the
28th. Final commercial values must be confirmed before commercial
launch.

------------------------------------------------------------------------

## 8. Risks and Dependencies

  -------------------------------------------------------------------------
  Risk / dependency       Impact                    Status
  ----------------------- ------------------------- -----------------------
  Privacy Decision 7      Blocks final provider     Open
                          disclosure and platform   
                          AI data-use wording       

  Permanent               Legal/data-minimization   Legal review pending
  active-account          concern                   
  retention                                         

  Fixed college fee       Commercial configuration  ₹3,000--₹4,000 range
                          and billing copy          

  BYOK fee                Commercial configuration  ₹40 proposed

  Cloud provider          Infrastructure design     Open

  Tier 1 backup           Near-zero RPO validation  Open
  implementation                                    

  Gemini free-tier        Recommended default may   Reverify before launch
  availability            change                    and periodically

  Provider quality        Answer/citation quality   Per-provider evaluation
  differences             varies                    

  Handwriting extraction  Lower extraction          Separate evaluation
                          confidence possible       

  Staff MFA omission      Accepted security risk    Fast-follow candidate
  -------------------------------------------------------------------------

------------------------------------------------------------------------

## 9. Open Decisions Register

  -----------------------------------------------------------------------
  ID                      Decision                Status
  ----------------------- ----------------------- -----------------------
  D7                      Platform policy on      Open
                          student data and AI     
                          model                   
                          training/improvement    

  D8                      Final fixed college     Open
                          monthly fee             

  D9                      Confirm BYOK            Open
                          per-student fee         

  D10                     Legal review of         Pending
                          permanent               
                          active-account          
                          conversation retention  

  D11                     Cloud provider          Open
                          selection               

  D12                     Tier 1 backup           Open
                          technology and          
                          frequency               

  D13                     Future trial-data       Deferred
                          carry-over policy       

  D14                     Staff MFA fast-follow   Deferred
                          timeline                

  D15                     Final provider-specific Depends on D7 and
                          disclosure wording      provider terms
  -----------------------------------------------------------------------

Quiz Battle traceability, cost treatment and the daily cap of three
battles are resolved and are not open decisions.

------------------------------------------------------------------------

## 10. Release Readiness Conditions

Before production launch: - Privacy Decision 7 is resolved. -
Provider-specific disclosures and acknowledgments are finalized. - Every
supported provider/model combination passes its own quality
evaluation. - Security and college-isolation test suites pass. - Load
testing validates the defined capacity. - Legal review of active-account
retention is completed. - Pricing is finalized. - Cloud hosting and
backup design are approved. - Document-processing and handwriting
evaluation are completed.

------------------------------------------------------------------------

## 11. Change Control and Approval

This PRD v1.3 is the product baseline for this revision. Product changes
must be recorded with their rationale and impact on the SRS. Items in
the Open Decisions Register remain unresolved until explicitly approved.

**Next governing document:** SRS v1.0.
