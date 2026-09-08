# Feature Specification: Ingredient Capture

**Feature Branch**: `main`

**Created**: 2026-09-08

**Status**: Draft

**Input**: User description: "Build the first core feature of a mobile cooking application: ingredient capture, AI-assisted recognition, review, correction, and confirmation. The feature must provide a real mobile user flow for turning a photo of available food ingredients into a reliable confirmed ingredient list."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Capture and confirm ingredients from a photo (Priority: P1)

A mobile user starts ingredient capture, grants camera access if needed, takes a photo of available food ingredients, reviews the photo, submits it for recognition, reviews the detected ingredients, corrects the list, and confirms the final ingredient list for later recipe recommendation features.

**Why this priority**: This is the core product value: converting what the user has in their kitchen into a reliable, user-verified ingredient list.

**Independent Test**: Can be fully tested by starting the capture flow with camera access available, taking a photo containing multiple ingredients, reviewing the image, submitting it once, editing at least one detected item, removing one incorrect item, adding one missing item, and confirming a non-empty valid list.

**Acceptance Scenarios**:

1. **Given** the user has camera access available, **When** they start ingredient capture and take a photo, **Then** they can review the captured image before submitting it for recognition.
2. **Given** the user is reviewing a captured image, **When** they choose to retake the photo, **Then** the previous captured image is discarded from the active draft and the user returns to capture.
3. **Given** the user submits a reviewed image for recognition, **When** recognition is in progress, **Then** the interface shows a clear loading state and prevents duplicate submissions.
4. **Given** recognition returns detected ingredients, **When** the review screen appears, **Then** the user can see that detections are editable suggestions and are not yet confirmed ingredients.
5. **Given** the user has a reviewed ingredient draft, **When** they confirm a valid non-empty final list, **Then** only the confirmed list becomes available to later recipe recommendation functionality.

---

### User Story 2 - Recover from permission, recognition, and processing problems (Priority: P2)

A mobile user encounters a blocked camera permission, recognition failure, connectivity problem, image processing problem, or empty detection result and receives clear guidance plus recovery actions such as retrying, retaking the photo, or manually entering ingredients.

**Why this priority**: The feature must remain usable when camera or AI recognition does not work, otherwise users cannot reliably create an ingredient list.

**Independent Test**: Can be tested by simulating denied camera permission, unavailable camera permission, failed recognition, no usable detections, and connectivity failure, then verifying that each state explains the issue and provides an appropriate next action.

**Acceptance Scenarios**:

1. **Given** camera permission has not been requested, **When** the user starts ingredient capture, **Then** the application explains why camera access is needed and offers the system permission request.
2. **Given** camera permission is denied and can be changed, **When** the user starts ingredient capture, **Then** the application explains that camera access is blocked and provides a path to recover through settings plus manual entry.
3. **Given** camera permission is unavailable on the device, **When** the user starts ingredient capture, **Then** the application explains that photo capture cannot be used and offers manual ingredient entry.
4. **Given** recognition fails or connectivity is unavailable, **When** the user is shown the error state, **Then** they can retry recognition, take another photo, or enter ingredients manually.
5. **Given** recognition returns no usable ingredients, **When** the result is shown, **Then** the application treats it as an empty result, explains what happened, and offers manual entry or another photo.

---

### User Story 3 - Manually create and validate an ingredient list (Priority: P3)

A mobile user can create the ingredient list manually without using or completing AI recognition, including adding ingredients, editing names, removing items, resolving duplicates, and confirming only when the list is valid and non-empty.

**Why this priority**: Manual entry is the required fallback that makes the feature dependable without successful AI recognition.

**Independent Test**: Can be tested by entering the flow without using a successful photo recognition result, adding multiple ingredients manually, attempting invalid confirmation, resolving validation issues, and confirming the final list.

**Acceptance Scenarios**:

1. **Given** the user chooses manual entry, **When** they add ingredient names, **Then** they can review and edit the draft list before confirmation.
2. **Given** the draft list is empty or contains only invalid ingredient names, **When** the user attempts to confirm, **Then** confirmation is blocked and the validation issue is visible.
3. **Given** the draft list contains duplicate or overlapping items, **When** the user reviews the list, **Then** they are prompted to resolve duplicates before final confirmation.

### Edge Cases

- Camera permission is denied permanently or can only be changed outside the application.
- Camera permission is temporarily unavailable because the device has no usable camera, the camera is restricted, or another condition prevents capture.
- The user cancels camera permission, photo capture, or image review before submitting.
- The captured photo is blurry, dark, obstructed, too large to process, or does not contain food ingredients.
- Recognition takes longer than expected, fails, times out, or loses connectivity.
- The user taps submit repeatedly while recognition is loading.
- Recognition returns duplicate, overlapping, misspelled, non-food, or low-quality ingredient suggestions.
- The user removes every detected ingredient during review.
- The user attempts to confirm an empty list, whitespace-only names, duplicate unresolved ingredients, or otherwise invalid entries.
- The user switches from AI-assisted review to manual entry after an error or empty result.
- The user leaves the flow before confirmation; unfinished drafts and temporary images must not become confirmed ingredients.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The application MUST provide an entry point from the mobile application into the ingredient capture flow.
- **FR-002**: The application MUST let the user take a photo containing one or more available food ingredients when camera access is available.
- **FR-003**: The application MUST clearly handle camera permission states before capture, including not-yet-requested, granted, denied with possible recovery, denied without in-app recovery, and unavailable camera access.
- **FR-004**: Permission messages MUST explain why camera access is needed in plain language and MUST offer manual ingredient entry whenever camera capture cannot proceed.
- **FR-005**: After a photo is captured, the application MUST show the captured image for user review before recognition submission.
- **FR-006**: While reviewing a captured image, the user MUST be able to retake the photo instead of submitting it.
- **FR-007**: The application MUST require an explicit user action to submit a reviewed image for recognition.
- **FR-008**: During image analysis, the application MUST show a clear loading state and MUST prevent accidental duplicate recognition submissions for the same reviewed image.
- **FR-009**: Recognition results MUST be presented as editable AI detections, not as final confirmed ingredients.
- **FR-010**: The review interface MUST make it obvious that the ingredient list can be edited before confirmation.
- **FR-011**: The user MUST be able to confirm correctly detected ingredients.
- **FR-012**: The user MUST be able to edit ingredient names before confirmation.
- **FR-013**: The user MUST be able to remove incorrect detected ingredients before confirmation.
- **FR-014**: The user MUST be able to manually add missing ingredients before confirmation.
- **FR-015**: The application MUST identify duplicate or overlapping ingredients in the review draft and require the user to resolve them when they would create an ambiguous final list.
- **FR-016**: The application MUST provide a manual-only path for creating an ingredient list without depending on camera capture or successful AI recognition.
- **FR-017**: The application MUST block confirmation when the final ingredient list is empty.
- **FR-018**: The application MUST block confirmation when any final ingredient is invalid, including blank names, whitespace-only names, or unresolved duplicate or overlapping entries.
- **FR-019**: Validation errors MUST be visible near the affected list or ingredient and MUST explain what the user can do to fix the issue.
- **FR-020**: If recognition fails, no usable ingredients are detected, the image cannot be processed, connectivity fails, or another recoverable problem occurs, the application MUST show a clear error or empty state with recovery actions.
- **FR-021**: Recovery actions MUST include the appropriate available options among retrying recognition, taking another photo, and entering ingredients manually.
- **FR-022**: Once the user confirms the ingredient list, only the final confirmed ingredient list MAY be made available to later features.
- **FR-023**: Raw AI detections, discarded ingredients, incomplete drafts, and temporary image state MUST NOT be treated as confirmed ingredients.
- **FR-024**: If the user exits the flow before confirmation, no draft ingredients or temporary image state MAY be made available as confirmed ingredients.
- **FR-025**: Primary controls for capture, permission recovery, image review, submission, retry, retake, manual entry, list editing, item removal, duplicate resolution, and final confirmation MUST have accessible labels and support accessible mobile interactions.
- **FR-026**: The application MUST provide visible loading, success, empty, permission, validation, and error states for the flow.
- **FR-027**: The confirmation success state MUST clearly indicate that the ingredient list has been confirmed and is ready for later recipe recommendation functionality.

### Key Entities

- **Captured Image**: A temporary photo selected for recognition review. It has a review status and may be submitted, retaken, or discarded before confirmation.
- **AI Detection**: A provisional recognition suggestion from the captured image. It includes an ingredient name candidate and may be accepted, edited, removed, merged, or ignored by the user.
- **Ingredient Draft Item**: An editable item in the review list before confirmation. It may originate from AI recognition or manual entry and must pass validation before confirmation.
- **Confirmed Ingredient**: A user-approved ingredient name that belongs to the final confirmed list and is eligible for later recipe recommendation functionality.
- **Ingredient List Draft**: The full editable collection of draft items during capture, recognition review, correction, duplicate resolution, or manual entry.
- **Confirmed Ingredient List**: The final non-empty validated collection created only after explicit user confirmation.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: At least 90% of test users can complete the photo capture, review, correction, and confirmation flow for a photo with 1-10 visible ingredients in under 3 minutes.
- **SC-002**: In 100% of completed AI-assisted sessions, later features receive only the user-confirmed ingredient list and never receive raw detections or discarded draft items.
- **SC-003**: In validation testing, duplicate recognition submissions do not create duplicate ingredient drafts or multiple active recognition results for the same reviewed image.
- **SC-004**: At least 95% of permission, loading, empty, validation, success, and recoverable error states show a user-visible explanation and at least one appropriate next action.
- **SC-005**: At least 90% of users who encounter a recoverable recognition failure can still create a confirmed ingredient list through retry, retake, or manual entry without restarting the application.
- **SC-006**: Users can create and confirm a manual ingredient list of up to 10 ingredients in under 2 minutes when camera capture or recognition is unavailable.
- **SC-007**: Accessibility review confirms that 100% of primary controls in the flow have understandable labels and can be operated through supported accessible mobile interactions.

## Assumptions

- The initial target user is a mobile app user preparing to get recipe recommendations from ingredients already available in their kitchen.
- Ingredient names are free-text food ingredient labels at this stage; quantities, units, freshness, brand, package size, and dietary preferences are outside this feature unless added later.
- The feature may display recognition confidence or uncertainty only when the recognition system provides reliable user-facing confidence information; otherwise no confidence values are shown.
- Temporary captured images and recognition drafts are retained only as long as needed for the active flow and are not considered confirmed user data.
- Later recipe recommendation functionality will consume only the confirmed ingredient list produced by this feature.
- Authentication, user profiles, recipe recommendation, recipe generation, favorites, history, and social features are outside this feature.
