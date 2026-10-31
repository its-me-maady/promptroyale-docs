# Software Requirements Specification (SRS)

**Product:** College Academic AI Platform ("Prompt Royale")\
**Version:** 1.0\
**Status:** Final baseline for this revision\
**Based on:** PRD v1.3 and SRS v0.4\
**Audience:** Product owner, engineering, QA, technical design and
operations

> **Document status note:** This SRS is the final requirements baseline
> for this revision and is ready for technical review/sign-off. Privacy
> Decision 7-dependent requirements remain explicitly unresolved. The
> business and infrastructure items listed in Section 9 remain open
> dependencies; their existence does not invalidate the requirements
> baseline but they must be resolved before the affected implementation
> or release activities.

------------------------------------------------------------------------

## 1. Purpose and Scope

This SRS translates the product requirements into testable system
behavior, logical data entities, workflows, validation rules,
permissions, non-functional requirements and acceptance criteria.

It does not define UI visual design, API contracts or
infrastructure-as-code. Those are subsequent technical-design
deliverables.

The system includes: - Student Android application. - Department User
web application. - College Administrator web application. - Internal
Vendor Support diagnostics capability. - College-specific backend/data
environment. - Integrations with supported external AI providers.

ERP and LMS functions excluded by the PRD are outside this SRS.

------------------------------------------------------------------------

## 2. System Actors

-   **Student:** Android application.
-   **Department User:** Web application.
-   **College Administrator:** Web application.
-   **Vendor Support:** Internal diagnostics-only capability.
-   **External AI Provider:** OpenRouter, Gemini, OpenAI, DeepSeek or
    Anthropic Claude, used through Platform-Managed or BYOK
    arrangements.

### 2.1 Role Restrictions

-   Students can access only their own account data and currently
    assigned subjects.
-   Department Users can manage authorized department students, subjects
    and documents, but have no routine access to individual student
    conversations or individual usage.
-   College Administrators can access college-level administration,
    aggregate usage and billing, but have no routine access to
    individual student conversations or individual usage.
-   Vendor Support is restricted to authorized diagnostics and has no
    routine conversation-content access.
-   Feedback reviewers can view only the content explicitly submitted
    through the feedback workflow.

------------------------------------------------------------------------

## 3. Core Data Entities

The following is the first-pass logical entity inventory. Data
normalization and physical schema are part of technical design.

  -----------------------------------------------------------------------
  Entity                              Purpose
  ----------------------------------- -----------------------------------
  College                             Deployment-scoped root; college
                                      identity, service tier and billing
                                      configuration

  Department                          Department belonging to a College

  Subject                             Subject belonging to a Department

  Document                            Academic document belonging to a
                                      Subject; format, size, page count,
                                      processing status and version chain

  StudentAccount                      Student identity, status and
                                      activation/deactivation dates

  DepartmentUserAccount               Department-level staff identity and
                                      role

  CollegeAdminAccount                 College-level administrative
                                      identity and role

  Subject Membership                  StudentAccount-to-Subject
                                      relationship, including added and
                                      removed timestamps

  Conversation                        Student-owned conversation,
                                      messages and citations

  Feedback                            Feedback associated with a specific
                                      message/answer

  AIProviderCredential                Student-owned BYOK credential,
                                      provider, encrypted key and status

  UsageRecord                         Platform-Managed token usage or
                                      BYOK key-health status

  BillingSnapshot                     College billing snapshot, date,
                                      active-student count and invoice

  BattleUsageCounter                  Lightweight daily battle-start
                                      counter for a StudentAccount
  -----------------------------------------------------------------------

### 3.1 Data Ownership and Isolation

-   College-owned records are scoped to the college deployment.
-   Students can access only their own conversations and credentials.
-   Subject documents are accessible only through current subject
    membership and authorized staff permissions.
-   College identity used for authorization must be derived from the
    authenticated session or trusted deployment context, not an
    untrusted client-supplied identifier.
-   Cross-college queries must be prevented.

### 3.2 Quiz Battle Persistence

Quiz Battle does not create a persistent quiz-history or leaderboard
entity. A lightweight BattleUsageCounter is permitted only to enforce
the daily battle-start limit. It stores no quiz content, answers, scores
or results.

------------------------------------------------------------------------

## 4. Functional Requirements

### 4.1 Authentication and Authorization

**FR-1.1** The system shall require authentication for every protected
endpoint. Unauthenticated requests shall return HTTP 401 and shall not
return protected data.

**FR-1.2** Every document-retrieval call shall re-check current Subject
Membership server-side at request time. Membership state cached at login
shall not be sufficient authorization.

**FR-1.3** Role permission checks shall be enforced at the API layer. A
direct API call made with insufficient permissions shall be rejected
with HTTP 403.

**FR-1.4** Cross-College access shall be structurally prevented. Every
college-scoped query shall use the College ID derived from the
authenticated session or trusted deployment context, never an untrusted
client-supplied parameter.

**Acceptance criteria:** - Unauthenticated protected requests are
rejected. - Removing a student's subject membership during a session
prevents retrieval on the next request. - Substituting another College
ID in a client request does not expose another college's data. - Users
cannot execute operations outside their assigned roles.

### 4.2 Account Activation and Lifecycle

**FR-2.1** The system shall support email-based student account
activation.

**FR-2.2** The activation flow shall require the student to create a
password.

**FR-2.3** The system shall not activate student accounts using a shared
or default password.

**FR-2.4** Student account states shall distinguish Active, Suspended
and Deactivated.

**FR-2.5** Deactivation shall immediately remove student access to
protected student functionality.

**Acceptance criteria:** - A valid activation link permits the intended
activation flow. - An invalid or expired activation link does not
activate an account. - Password creation is required during
activation. - Deactivated students cannot access protected student
functionality. - Suspended and deactivated states are distinguishable.

### 4.3 Department, Subject and Membership Management

**FR-3.1** A College Administrator shall be able to create and manage
departments within the college.

**FR-3.2** A Department User shall be able to create and manage subjects
within the authorized department.

**FR-3.3** A Department User shall be able to assign students to
subjects within the authorized scope.

**FR-3.4** A Department User shall be able to remove students from
subjects.

**FR-3.5** Subject Membership shall record added and removed timestamps.

**FR-3.6** Subject access shall be based on current membership, not
semester or academic-year classification.

**FR-3.7** The system shall support students assigned to arrear subjects
or subjects across different academic periods.

**Acceptance criteria:** - A student can access a currently assigned
subject. - A student cannot access an unassigned subject. - Removing
membership prevents retrieval on the next request. - Department Users
cannot manage subjects outside their authorized department.

### 4.4 Document Management

**FR-4.1** Upload shall accept PDF, scanned PDF, DOCX, TXT, Markdown,
PNG and JPEG.

**FR-4.2** Upload shall reject files larger than 25 MB.

**FR-4.3** Upload shall reject PDFs containing more than 200 pages.

**FR-4.4** Validation failures shall return specific, actionable error
messages.

**FR-4.5** Each document shall have one of the following processing
states: Pending, Processing, Available or Failed.

**FR-4.6** The uploading Department User shall be able to view
document-processing status.

**FR-4.7** A replacement document shall not become available for
retrieval until processing succeeds.

**FR-4.8** The prior available version shall remain available while a
replacement is processing and until the replacement is confirmed
Available.

**FR-4.9** Scanned and handwritten documents shall carry a
processing-confidence indicator.

**FR-4.10** Low-confidence extraction shall not be represented as fully
reliable source content without an appropriate indication.

**Acceptance criteria:** - Unsupported formats are rejected. - Files
over 25 MB and PDFs over 200 pages are rejected. - Successful uploads
enter the processing lifecycle. - Failed replacement processing does not
remove the prior available version. - Handwriting samples are included
in a separate evaluation set.

### 4.5 Academic Question Answering

**FR-5.1** A student's question shall retrieve only from documents
belonging to subjects in which the student currently has membership.

**FR-5.2** Subject membership shall be revalidated for each retrieval
request.

**FR-5.3** Every generated academic answer shall include citations
referencing supporting source documents.

**FR-5.4** If retrieved material does not sufficiently support an
answer, the system shall explicitly communicate insufficient evidence
rather than generate an unsupported answer.

**FR-5.5** The system shall not fabricate source citations.

**FR-5.6** AI requests shall be routed according to the configured
service arrangement and, for BYOK, the student's configured provider
credential.

**FR-5.7** A BYOK student without a configured credential shall receive
a provider setup prompt rather than a generic request failure.

**FR-5.8** Provider authentication failures, rate limits and
unavailability shall be surfaced as distinguishable errors.

**FR-5.9** Answer correctness and citation validity shall be evaluated
independently for each supported provider/model combination.

**Acceptance criteria:** - Retrieval uses only currently accessible
subjects. - Answers contain source citations. - Insufficient evidence
produces an explicit response. - Provider authentication, rate-limit and
availability errors are distinguishable. - Each supported provider/model
combination is evaluated against the quality targets.

### 4.6 Conversation Management

**FR-6.1** The system shall store conversation history belonging to the
student.

**FR-6.2** Conversation-history display shall be independent of AI
context assembly.

**FR-6.3** Only a bounded, permission-filtered subset of conversation
history may be sent to the AI provider as context for a new request.

**FR-6.4** A student shall be able to delete an individual conversation.

**FR-6.5** A student shall be able to clear all conversation history.

**FR-6.6** Deleted conversations shall be excluded from the active
application view immediately.

**FR-6.7** Permanent deletion shall follow the retention schedule in
Section 4.8.

**FR-6.8** On Subject Membership removal, citations and source previews
in existing conversations that point to now-restricted documents shall
display **"Source no longer available."**

**FR-6.9** Existing conversation history shall not be used to bypass
current subject-access restrictions.

**Acceptance criteria:** - A student can access only their own
conversations. - Deleted conversations disappear from the active view
immediately. - A follow-up question after membership removal retrieves
no material from the removed subject. - Restricted source previews do
not continue to expose source content.

### 4.7 BYOK Credential Management

**FR-7.1** The system shall support the configured BYOK providers:
OpenRouter, Gemini, OpenAI, DeepSeek and Anthropic Claude.

**FR-7.2** Gemini shall be presented first as the recommended provider
for a student without a configured provider credential, subject to
launch-time verification of availability and applicable terms.

**FR-7.3** Credentials shall be encrypted at rest.

**FR-7.4** Credentials shall be decrypted only in memory when required
for an authorized provider call.

**FR-7.5** Credentials shall never be written to application or
diagnostic logs in plaintext.

**FR-7.6** A student shall be able to revoke or replace their own
credential.

**FR-7.7** Revocation shall immediately block further AI calls using
that credential.

**FR-7.8** Each supported provider shall have its own disclosure text
shown and acknowledged before that provider's credential can be saved.

**FR-7.9** Final provider disclosure wording is dependent on Privacy
Decision 7 and provider-specific terms. This requirement remains
unresolved until those are approved.

**Acceptance criteria:** - Credentials are stored encrypted. - Automated
log checks detect no plaintext credential leakage. - One student's
request cannot use another student's stored credential. - Revocation
prevents subsequent calls using the revoked credential. - Credential
setup cannot complete without the required provider acknowledgment.

### 4.8 Retention and Deletion

**FR-8.1** Active-account conversation data shall be retained without
automatic time-based deletion under the current policy.

**FR-8.2** The active-account retention policy shall be configurable to
support a future policy change following legal review.

**FR-8.3** On student deactivation, student access shall be removed
immediately.

**FR-8.4** Conversation data belonging to a deactivated student shall be
purged after two months.

**FR-8.5** Backups containing deleted data shall be purged six months
after the original deletion event.

**FR-8.6** BYOK credentials shall be deleted at the moment of student
deactivation, not merely marked inactive.

**FR-8.7** Student-requested conversation deletion shall remove the
content from the active application view immediately and follow the
applicable permanent-deletion process.

**Acceptance criteria:** - Deactivation immediately blocks student
access. - Deactivated-account conversations are purged at the two-month
boundary. - Backups containing deleted data are purged at the six-month
boundary from the original deletion event. - BYOK credentials are
deleted at deactivation. - Automated retention tests verify the
specified time boundaries.

**Dependency:** Legal review of permanent active-account retention
remains open.

### 4.9 Usage, Billing and Service Tiers

**FR-9.1** Platform-Managed usage shall track input and output token
counts per student, including retrieved context included in the AI
request.

**FR-9.2** BYOK usage records shall track provider-key health status
only; they shall not repurpose Platform-Managed token-count fields.

**FR-9.3** The billing snapshot shall run on the 28th of each calendar
month.

**FR-9.4** Students activated after the snapshot shall be billed from
the next billing period.

**FR-9.5** Students deactivated after the snapshot shall remain billable
for the current billing period.

**FR-9.6** The system shall not apply proration under the current
billing policy.

**FR-9.7** An unpaid invoice shall cause student access to be suspended
three days after the due date if payment remains unpaid. Suspension is
distinct from deactivation.

**FR-9.8** Suspended students shall remain in the next billing snapshot
unless separately deactivated.

**FR-9.9** Trial accounts shall expire after 14 days.

**FR-9.10** On trial expiry without conversion, trial-created documents,
conversations and configuration shall be deleted/reset rather than
archived.

**FR-9.11** Conversion from trial to paid service shall start fresh;
trial data shall not carry over in version 1.

**Acceptance criteria:** - Billing snapshots use the 28th. -
Activation/deactivation on either side of the snapshot produces the
specified billing outcome. - No proration is applied. - Suspension is
distinct from deactivation. - Trial expiry removes trial-created data. -
Platform-Managed and BYOK usage records follow their distinct rules.

**Commercial values carried from the PRD:** - Platform-Managed: ₹100 per
active student per month. - BYOK: ₹40 per active student per month
proposed; confirmation pending. - Fixed college fee: ₹3,000--₹4,000 per
month; final single value pending.

### 4.10 Feedback

**FR-10.1** The system shall allow a student to submit feedback about a
specific AI answer.

**FR-10.2** Before submission is finalized, the system shall disclose
exactly what conversation content will be shared with reviewers.

**FR-10.3** Feedback-visible content shall be limited to the submitted
message/answer pair and associated feedback.

**FR-10.4** The feedback workflow shall not expose the student's full
conversation history to reviewers.

**Acceptance criteria:** - The required disclosure is shown before
feedback submission. - Reviewers can access only the submitted
content. - Feedback access does not grant access to unrelated
conversations.

### 4.11 Quiz Battle --- Solo Score/Time Attack

**FR-11.1** A student shall initiate a battle by selecting one subject
in which the student currently has membership.

**FR-11.2** Subject eligibility shall use the live membership check in
FR-1.2, not cached login state.

**FR-11.3** The system shall generate exactly ten multiple-choice
questions per battle.

**FR-11.4** Questions shall be generated exclusively from documents
within the selected subject.

**FR-11.5** Each question shall carry a source citation, using the same
citation requirements as regular academic answers.

**FR-11.6** A battle shall use a single difficulty level; multiple
difficulty tiers are not part of the feature.

**FR-11.7** If subject content is insufficient to generate ten valid
questions, the system shall display an explicit "not enough material in
this subject" message rather than fabricate questions.

**FR-11.8** A battle session shall be timed.

**FR-11.9** The final score shall be calculated from correct answers out
of ten and time taken.

**FR-11.10** Battle results shall be displayed only within the live
session.

**FR-11.11** Battle results shall not be written to conversation
history, persistent quiz history or leaderboard data.

**FR-11.12** A lightweight BattleUsageCounter may be persisted solely to
enforce the daily battle-start limit; it shall contain no quiz content,
answers, scores or results.

**FR-11.13** If Subject Membership is revoked while a battle is in
progress, the battle shall terminate immediately.

**FR-11.14** Each question-generation call shall use the normal AI
provider arrangement. Platform-Managed calls count as token usage; BYOK
calls use the student's configured provider credential.

**FR-11.15** No separate Quiz Battle fee shall be charged.

**FR-11.16** A student shall be limited to **3 battles started per
calendar day**, based on the college's local time.

**FR-11.17** On reaching the cap, the system shall show an explicit
daily-limit message and reset time. The fourth attempt within the same
college-local calendar day shall be blocked.

**Acceptance criteria:** - Every battle contains exactly ten valid
questions. - Every question has a source citation. - Insufficient source
material prevents the battle from proceeding with fabricated
questions. - Membership removal terminates an active battle. - Quiz
results are not persisted after the live session. - The fourth daily
attempt is blocked with the correct reset information. - The daily
counter resets at the next college-local day boundary. - Quiz generation
is included in provider-quality evaluation.

------------------------------------------------------------------------

## 5. Non-Functional Requirements

### 5.1 Recovery and Backup

**NFR-1** Recovery Time Objective shall not exceed 4 hours.

**NFR-2** Tier 1 data --- Documents, Subjects, Accounts and Billing
records --- shall have a near-zero Recovery Point Objective.

**NFR-3** Tier 2 conversation data may tolerate loss within the agreed
recovery window.

**NFR-4** Backup frequency shall distinguish Tier 1 and Tier 2 data
rather than apply one undifferentiated schedule.

**NFR-5** BattleUsageCounter is low-value enforcement data and may use a
less stringent recovery policy.

### 5.2 Authentication Hardening

**NFR-6** Staff MFA is not required in version 1. This is an accepted
risk. The architecture should not preclude adding MFA later without
redesign.

### 5.3 Capacity

**NFR-7** Each college deployment shall support the planning capacity of
150 student accounts.

**NFR-8** Each college deployment shall support up to 100 concurrent
users.

**NFR-9** Load testing shall be completed before production sign-off.

Quiz Battle is solo/async and introduces no separate real-time
concurrency requirement beyond the overall capacity target. Its daily
cap bounds the maximum start frequency per student.

### 5.4 Compatibility

**NFR-10** The minimum supported Android version shall be Android 8.0
(API 26).

**NFR-11** Department and College Administrator interfaces shall support
modern web browsers.

**NFR-12** English shall be the supported launch language.

### 5.5 AI Quality

**NFR-13** The system shall achieve at least 90% answer correctness on
the agreed evaluation set.

**NFR-14** The system shall achieve at least 95% citation validity on
the agreed evaluation set.

**NFR-15** Each supported provider/model combination shall be evaluated
independently.

**NFR-16** Handwriting and scanned-document extraction shall be
evaluated separately.

### 5.6 Hosting

**NFR-17** Each college shall operate in a separate cloud-hosted
environment.

**NFR-18** The cloud provider remains to be selected. A cost and
capability comparison shall be completed before infrastructure
implementation begins.

------------------------------------------------------------------------

## 6. Privacy and Access-Control Requirements

  -----------------------------------------------------------------------------
  Data /          Student        Department     College          Vendor Support
  operation                      User           Administrator    
  --------------- -------------- -------------- ---------------- --------------
  Own             Yes            No routine     No routine       No routine
  conversations                  access         access           access

  Other students' No             No             No               No
  conversations                                                  

  Own BYOK        Yes            No             No routine       No routine
  credentials                                   access           access

  Subject         Through        Authorized     College          Diagnostics
  documents       current        department     administrative   only
                  membership     scope          scope as         
                                                permitted        

  Aggregate usage Own usage      No individual  Yes              Operational
                  where exposed  usage                           diagnostics

  Individual      Own usage      No             No               No routine
  usage           where exposed                                  access

  Submitted       Own            Only           Only authorized  No routine
  feedback        submissions    authorized     submitted        access
                                 submitted      content          
                                 content                         
  -----------------------------------------------------------------------------

Feedback access is limited to content explicitly submitted by the
student. The system shall not introduce routine staff access to student
conversation content.

------------------------------------------------------------------------

## 7. External AI Provider Requirements

The version 1 provider list is OpenRouter, Gemini, OpenAI, DeepSeek and
Anthropic Claude.

Provider integrations shall: - Route requests according to the
configured service arrangement. - Handle provider authentication
failures, rate limits and unavailability. - Preserve citation
requirements. - Prevent one student's BYOK credential from being used
for another student. - Apply provider-specific disclosure and
acknowledgment requirements.

The platform's own AI training policy is distinct from each external
provider's data-handling policy.

**Privacy Decision 7 remains open.** Provider disclosures and
requirements that depend on it shall not be treated as finalized until
the decision is approved.

------------------------------------------------------------------------

## 8. Traceability Matrix

  PRD area                                                 SRS section
  -------------------------------------------------------- -------------
  Authentication and account activation                    4.1, 4.2
  College, department, subject and membership management   4.3
  Document management                                      4.4
  Academic question answering                              4.5
  Conversation management                                  4.6
  BYOK credential management                               4.7
  Retention and deletion                                   4.8
  Usage, billing and tiers                                 4.9
  Feedback                                                 4.10
  Quiz Battle                                              4.11
  Security and access control                              4.1, 6, 7
  Capacity and compatibility                               5
  Recovery and hosting                                     5

------------------------------------------------------------------------

## 9. Open Items Carried from the PRD

1.  **Privacy Decision 7 --- AI training/model-improvement policy:**
    blocks final provider disclosure wording and dependent data-use
    requirements.
2.  **Fixed college fee:** currently ₹3,000--₹4,000 per month; a single
    value is not pinned. This affects commercial configuration and
    billing copy.
3.  **BYOK per-student fee:** ₹40 is proposed and requires confirmation.
4.  **Legal review of permanent active-account conversation retention:**
    pending.
5.  **Cloud provider selection:** required before infrastructure
    implementation.
6.  **Tier 1 backup technology and frequency:** a specific technical
    proposal is required to meet the near-zero RPO requirement.
7.  **Provider-specific disclosure wording:** depends on Privacy
    Decision 7 and the applicable provider terms.
8.  **Staff MFA follow-up timeline:** deferred; not required for version
    1.

### 9.1 Quiz Battle Open Items --- Resolved

-   PRD traceability: resolved; Quiz Battle is included in the PRD.
-   AI cost treatment: resolved; generation is included in the existing
    Platform-Managed/BYOK usage arrangement, with no separate fee.
-   Daily cap: confirmed at 3 battles per student per college-local
    calendar day.

No open parameters remain for Quiz Battle in this SRS.

------------------------------------------------------------------------

## 10. Technical Design and Test Planning

Following review and approval:

1.  Normalize the logical data model.
2.  Define API contracts and endpoint-level authorization.
3.  Complete the cloud-provider cost and capability comparison.
4.  Design college isolation and deployment architecture.
5.  Define backup and recovery implementation for Tier 1 and Tier 2
    data.
6.  Expand acceptance criteria into the full security test suite.
7.  Define provider-specific AI quality evaluation datasets for all five
    provider options.
8.  Define document extraction and handwriting evaluation.
9.  Define Quiz Battle question-generation, citation and daily-cap
    tests.
10. Finalize provider disclosures after Privacy Decision 7 is resolved.

------------------------------------------------------------------------

## 11. Approval and Change Control

This SRS v1.0 is the requirements baseline for this revision.
Requirements changes must be recorded with their rationale and impact on
the PRD, SRS, technical design and test plan.

Open items in Section 9 remain unresolved until explicitly approved.
Technical implementation must not silently decide product, privacy or
commercial policy.

**Status:** Final baseline for this revision; ready for technical review
and sign-off.
