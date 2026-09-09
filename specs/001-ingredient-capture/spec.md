# Feature Specification: Ingredient Capture

**Feature Branch**: `main`

**Created**: 2026-09-08

**Status**: Draft

**Input**: User description: "Build the first core feature of a mobile cooking application: ingredient capture, AI-assisted recognition, review, correction, and confirmation. The feature must provide a real mobile user flow for turning a photo of available food ingredients into a reliable confirmed ingredient list."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Capture or select an image and confirm ingredients (Priority: P1)

A mobile user starts ingredient capture, chooses to take a new photo or select an existing image, reviews the image, submits it for recognition, reviews the editable detected ingredient list, corrects it through Spanish catalog suggestions, and confirms a final canonical ingredient list.

**Why this priority**: This is the core product value: converting what the user has in their kitchen into a reliable, user-verified list that later recipe functionality can trust.

**Independent Test**: Can be fully tested by starting the flow, choosing camera or gallery, reviewing an image with multiple common ingredients, submitting it once, editing one item through catalog suggestions, removing one item, adding one missing item, and confirming a valid non-empty list of canonical ingredient names.

**Acceptance Scenarios**:

1. **Given** the user starts ingredient capture, **When** they choose camera capture and camera access is available, **Then** they can take a photo and review the captured image before submitting it for recognition.
2. **Given** the user starts ingredient capture, **When** they choose gallery selection, **Then** they can select one image through the system image picker and review that image before submitting it for recognition.
3. **Given** the user is reviewing an image from camera or gallery, **When** they choose to retake or reselect, **Then** the current unsubmitted image is discarded from the active draft and the user returns to image selection.
4. **Given** the user submits a reviewed image for recognition, **When** recognition is in progress, **Then** the interface shows a clear loading state and prevents duplicate submissions for that image.
5. **Given** recognition returns confirmable ingredients, **When** the review screen appears, **Then** the user sees an editable list of canonical Spanish ingredient names that are included by default but not final until the user confirms the list.
6. **Given** the user confirms a valid non-empty final list, **When** confirmation succeeds, **Then** the confirmed list replaces any previous locally stored confirmed list and becomes the only ingredient list available to later recipe functionality.

---

### User Story 2 - Recover from permission, recognition, and processing problems (Priority: P2)

A mobile user encounters blocked camera permission, unavailable camera access, gallery selection cancellation, recognition failure, connectivity failure, image processing failure, timeout, or empty recognition and receives clear guidance plus recovery actions such as retrying once, taking another photo, selecting another image, or entering ingredients manually.

**Why this priority**: The feature must remain usable when camera, gallery, or AI recognition does not work; otherwise users cannot reliably create an ingredient list.

**Independent Test**: Can be tested by simulating denied camera permission, unavailable camera access, canceled gallery selection, failed recognition, no usable detections, long recognition, and connectivity failure, then verifying that each state explains the issue and provides an appropriate next action.

**Acceptance Scenarios**:

1. **Given** camera permission has not been requested, **When** the user chooses camera capture, **Then** the application explains why camera access is needed and offers the system permission request.
2. **Given** camera permission is denied and can be changed, **When** the user chooses camera capture, **Then** the application explains that camera access is blocked and provides a path to recover through settings plus non-camera alternatives.
3. **Given** camera access is unavailable, **When** the user chooses camera capture, **Then** the application explains that photo capture cannot be used and offers gallery selection and manual ingredient entry.
4. **Given** recognition exceeds 30 seconds, **When** the user chooses not to wait, **Then** they can cancel the active recognition attempt and continue with manual entry; any late recognition response is ignored.
5. **Given** recognition fails or connectivity is unavailable, **When** the user is shown the error state, **Then** they can retry recognition once for the same image, take another photo, select another image, or enter ingredients manually.
6. **Given** recognition returns no confirmable ingredients after filtering and normalization, **When** the result is shown, **Then** the application treats it as an empty result and offers camera, gallery, and manual recovery actions.

---

### User Story 3 - Manually create a canonical ingredient list (Priority: P3)

A mobile user can create the ingredient list manually from the start of the flow without using AI recognition, searching a Spanish ingredient catalog, selecting valid suggestions, removing items, and confirming only when the list is valid, non-empty, deduplicated, and within the list limit.

**Why this priority**: Manual entry is the required fallback that makes the feature dependable without successful camera capture, gallery selection, or AI recognition.

**Independent Test**: Can be tested by choosing manual entry from the initial flow screen, searching for catalog ingredients, adding multiple valid items, attempting invalid confirmation, resolving validation issues, and confirming the final list.

**Acceptance Scenarios**:

1. **Given** the user chooses manual entry from the initial flow screen, **When** they type at least 2 characters, **Then** the application shows matching Spanish catalog suggestions that can be selected into the draft list.
2. **Given** the draft list is empty or contains no valid canonical ingredients, **When** the user attempts to confirm, **Then** confirmation is blocked and the validation issue is visible.
3. **Given** the draft list has reached 20 ingredients, **When** the user tries to add another ingredient, **Then** the application prevents the addition and explains the list limit.
4. **Given** the user searches for an ingredient not available in the catalog, **When** no suggestion exists, **Then** the application says it cannot find that ingredient and asks the user to try another name or choose a suggestion.

### Edge Cases

- Camera permission is denied permanently or can only be changed outside the application.
- Camera access is unavailable because the device has no usable camera, the camera is restricted, or another condition prevents capture.
- The user cancels camera permission, photo capture, gallery selection, image review, recognition, or manual entry before confirmation.
- The selected image is blurry, dark, obstructed, too large to process, or does not contain food ingredients.
- Recognition takes longer than 30 seconds, fails, times out, loses connectivity, or returns after the user canceled the attempt.
- The user taps submit repeatedly while recognition is loading.
- Recognition returns duplicate, overlapping, misspelled, non-food, non-Spanish, unsupported, overly vague, or low-quality ingredient suggestions.
- Recognition returns a mix of confirmable ingredients and unrecognized elements.
- Recognition returns only unrecognized elements after filtering non-ingredients and normalizing known ingredients.
- A detected specific variant is known by the catalog and should be preserved as a distinct canonical ingredient.
- A detected specific variant is unknown but has a clear generic catalog match and should be included as that generic ingredient.
- A detected item has no clear catalog match and should be excluded from the review list.
- The user removes every ingredient during review.
- The user attempts to confirm an empty list, more than 20 ingredients, unresolved invalid entries, or otherwise invalid entries.
- The user attempts to add a duplicate manually or edits an ingredient into a duplicate.
- The user starts a new confirmed list when a previous confirmed list already exists locally.
- The user leaves the flow before confirmation; unfinished drafts and temporary images must not become confirmed ingredients.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The application MUST provide an entry point from the mobile application into the ingredient capture flow.
- **FR-002**: The initial flow screen MUST offer three paths: take a photo, choose an existing image, or enter ingredients manually.
- **FR-003**: The application MUST let the user take a photo containing one or more available food ingredients when camera access is available.
- **FR-004**: The application MUST let the user choose one existing image through a system image picker without requiring broad gallery browsing access inside the application.
- **FR-005**: The application MUST clearly handle camera permission states before capture, including not-yet-requested, granted, denied with possible recovery, denied without in-app recovery, and unavailable camera access.
- **FR-006**: Permission messages MUST explain why camera access is needed in plain language and MUST offer gallery selection and manual ingredient entry whenever camera capture cannot proceed.
- **FR-007**: After a photo is captured or an image is selected, the application MUST show the image for user review before recognition submission.
- **FR-008**: While reviewing an image, the user MUST be able to retake or reselect instead of submitting it.
- **FR-009**: The application MUST require an explicit user action to submit a reviewed image for recognition.
- **FR-010**: During image analysis, the application MUST show a clear loading state and MUST prevent accidental duplicate recognition submissions for the same reviewed image.
- **FR-011**: If recognition remains unresolved after 30 seconds, the application MUST allow the user to cancel the active recognition attempt and continue with manual entry.
- **FR-012**: If a canceled recognition attempt later returns a result, the application MUST ignore that result and MUST NOT merge it into the current manual draft or confirmed list.
- **FR-013**: If recognition fails for a recoverable reason, the application MUST allow no more than one retry for the same image before requiring the user to take another photo, choose another image, or enter ingredients manually.
- **FR-014**: Recognition results MUST be treated as editable suggestions and MUST NOT become confirmed ingredients until the user confirms the final list.
- **FR-015**: The review interface MUST make it obvious that the ingredient list can be edited before confirmation.
- **FR-016**: The application MUST filter non-food and non-ingredient detections before presenting the review list.
- **FR-017**: The application MUST use an MVP Spanish catalog of 200 common household cooking ingredients as the only source of confirmable ingredient names.
- **FR-018**: Confirmed ingredients MUST be canonical Spanish ingredient names from the catalog; free-text names that do not resolve to the catalog MUST NOT be confirmable.
- **FR-019**: The MVP catalog MUST cover a realistic broad pantry, including fresh foods, staple basics, common simple processed ingredients, preserves, sauces, and frozen ingredients.
- **FR-020**: The MVP catalog MUST exclude prepared complete dishes and ready-to-eat meals as confirmable ingredients.
- **FR-021**: The catalog SHOULD preserve specific common variants as distinct canonical ingredients when available.
- **FR-022**: When recognition detects an unknown specific variant with a clear generic catalog match, the application MUST include the generic canonical ingredient in the review list without requiring a separate user approval step for that downgrade.
- **FR-023**: When recognition detects an item with no clear catalog match, the application MUST exclude it from the review list.
- **FR-024**: If recognition returns both confirmable ingredients and excluded unrecognized elements, the application MUST show the confirmable ingredients and a general notice that some items could not be recognized.
- **FR-025**: The notice for unrecognized elements MUST provide a direct action to add missing ingredients manually through catalog search.
- **FR-026**: If recognition returns no confirmable ingredients after filtering and normalization, the application MUST show an empty or error state rather than a review list of unresolved detections.
- **FR-027**: The user MUST be able to remove detected ingredients from the draft list before confirmation.
- **FR-028**: The user MUST be able to edit an ingredient by searching the catalog and selecting a valid suggestion.
- **FR-029**: The user MUST be able to manually add missing ingredients by searching the catalog and selecting a valid suggestion.
- **FR-030**: Catalog search for manual entry or editing MUST begin showing suggestions after the user enters at least 2 characters.
- **FR-031**: If catalog search has no matching ingredient, the application MUST state that the ingredient was not found and prompt the user to try another name or choose a suggestion.
- **FR-032**: The application MUST deduplicate equivalent ingredients automatically after recognition normalization, before showing the review list.
- **FR-033**: If the user manually tries to add an ingredient that already exists in the draft list, the application MUST leave the list unchanged without adding a duplicate.
- **FR-034**: If the user edits an existing ingredient into another ingredient already in the draft list, the application MUST automatically merge the duplicate so only one canonical ingredient remains.
- **FR-035**: The draft and confirmed ingredient lists MUST preserve detection and entry order, with manually added ingredients appended after existing items unless duplicate merging removes an item.
- **FR-036**: The user MUST confirm the list as a whole; valid detected ingredients are included by default unless the user edits or removes them.
- **FR-037**: The application MUST provide a manual-only path for creating an ingredient list without depending on camera capture, gallery selection, or successful AI recognition.
- **FR-038**: The application MUST block confirmation when the final ingredient list is empty.
- **FR-039**: The application MUST block confirmation when the final ingredient list contains more than 20 ingredients.
- **FR-040**: The application MUST block confirmation when any final ingredient is invalid, not selected from the catalog, or otherwise unresolved.
- **FR-041**: Validation errors MUST be visible near the affected list or ingredient and MUST explain what the user can do to fix the issue.
- **FR-042**: If recognition fails, no usable ingredients are detected, the image cannot be processed, connectivity fails, or another recoverable problem occurs, the application MUST show a clear error or empty state with recovery actions.
- **FR-043**: Recovery actions MUST include the appropriate available options among retrying recognition, taking another photo, choosing another image, and entering ingredients manually.
- **FR-044**: If the user cancels the flow before confirmation, the application MUST discard the active draft and image state without saving them as confirmed ingredients.
- **FR-045**: Once the user confirms the ingredient list, only the final confirmed ingredient list MAY be made available to later features.
- **FR-046**: Raw AI detections, excluded detections, discarded ingredients, incomplete drafts, and temporary image state MUST NOT be treated as confirmed ingredients.
- **FR-047**: The confirmed ingredient list MUST be stored locally on the device without requiring authentication or a user profile.
- **FR-048**: A newly confirmed ingredient list MUST replace any previously confirmed local ingredient list without warning or additional confirmation.
- **FR-049**: The feature MUST NOT provide direct editing of a previously confirmed list after the user exits the flow; a later correction requires creating and confirming a new list.
- **FR-050**: After confirmation, the application MUST show a success state with a primary action labeled "Buscar recetas".
- **FR-051**: In this MVP, activating "Buscar recetas" MUST close the ingredient capture flow or return to the starting area with the confirmed list saved locally; this feature MUST NOT generate or display recipe recommendations.
- **FR-052**: Temporary captured or selected images from successful recognition sessions MUST NOT be retained after the active flow ends.
- **FR-053**: Images associated with failed, empty, or technically problematic recognition attempts MAY be retained temporarily for debugging, but MUST be deleted within 24 hours and MUST NOT be shown as user history.
- **FR-054**: Image retention for debugging MUST rely on the application's general privacy policy rather than a separate in-flow consent prompt.
- **FR-055**: Primary controls for capture, gallery selection, permission recovery, image review, submission, retry, cancel, retake, reselect, manual entry, catalog search, list editing, item removal, final confirmation, and post-confirmation action MUST have accessible labels and a logical accessibility order.
- **FR-056**: Loading, error, validation, empty, and success states MUST be available to supported assistive technologies.
- **FR-057**: Primary touch controls MUST meet a verifiable minimum touch target size and visible text or controls MUST meet verifiable contrast expectations for mobile use.
- **FR-058**: The application MUST provide visible loading, success, empty, permission, validation, and error states for the flow.
- **FR-059**: The MVP catalog MUST start from an internally curated list and MAY be adjusted during testing when common missing ingredients or synonyms appear repeatedly and affect realistic ingredient capture sessions.

### Key Entities

- **Selected Image**: A temporary photo from camera capture or system gallery selection. It has an origin, a review status, and may be submitted, replaced, canceled, or discarded before confirmation.
- **AI Detection**: A provisional recognition suggestion derived from the selected image. It is never confirmed data and may be filtered, normalized, deduplicated, excluded, or converted into a draft item.
- **Canonical Ingredient**: A valid Spanish ingredient name from the MVP catalog. It may represent a generic ingredient or a specific known variant.
- **Ingredient Draft Item**: An editable canonical ingredient in the review list before confirmation. It may originate from recognition or manual catalog selection and must remain valid before confirmation.
- **Ingredient List Draft**: The ordered, editable collection of draft items during recognition review or manual entry. It is discarded if the user exits before confirmation.
- **Confirmed Ingredient List**: The final non-empty ordered collection of 1-20 canonical ingredients created only after explicit user confirmation and stored locally for later recipe functionality.
- **Temporary Debug Image**: A failed, empty, or technically problematic recognition image retained only for operational debugging and deleted within 24 hours.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: At least 90% of test users can complete the image selection, recognition review, correction, and confirmation flow for an image with 1-10 visible common ingredients in under 3 minutes.
- **SC-002**: Users can create and confirm a manual ingredient list of up to 10 common ingredients in under 2 minutes when camera capture, gallery selection, or recognition is unavailable.
- **SC-003**: In 100% of completed sessions, later features receive only the user-confirmed canonical ingredient list and never receive raw detections, excluded detections, discarded draft items, or temporary image state.
- **SC-004**: In validation testing, duplicate recognition submissions do not create duplicate ingredient drafts or multiple active recognition results for the same reviewed image.
- **SC-005**: In recognition tests, equivalent detected ingredients are deduplicated before review so users see at most one item for each canonical ingredient.
- **SC-006**: In at least 75% of successful AI-assisted test sessions with 1-10 common ingredients visible, the user confirms the list after editing, removing, or adding 2 ingredients or fewer.
- **SC-007**: At least 95% of permission, loading, empty, validation, success, and recoverable error states show a user-visible explanation and at least one appropriate next action.
- **SC-008**: At least 90% of users who encounter a recoverable recognition failure can still create a confirmed ingredient list through retry, new photo, gallery selection, or manual entry without restarting the application.
- **SC-009**: Accessibility review confirms that 100% of primary controls have understandable labels, logical navigation order, accessible state messaging, minimum touch target size, and readable contrast.
- **SC-010**: Privacy review confirms that successful-session images are not retained after the active flow ends and retained failed/problem images are deleted within 24 hours.

## Assumptions

- The initial target user is a Spanish-language mobile app user preparing to get recipe recommendations from ingredients already available in their kitchen.
- Ingredient names are canonical Spanish catalog entries only; quantities, units, freshness, brand, package size, dietary preferences, and user-defined custom ingredients are outside this feature.
- The MVP catalog contains 200 common household cooking ingredients in Spanish, including common Spanish synonyms and selected specific variants when useful, but it does not attempt comprehensive international or multilingual coverage.
- Catalog coverage reflects a realistic broad pantry: fresh foods, staple basics, simple processed ingredients commonly used as ingredients, preserves, sauces, and frozen ingredients.
- Prepared complete dishes and ready-to-eat meals are not confirmable ingredients in this MVP.
- The catalog starts as an internally curated list and is adjusted only when common missing ingredients or synonyms appear repeatedly in recognition or catalog-search testing.
- Recognition confidence values or uncertainty indicators are not shown in the MVP, even if the recognition system has internal confidence information.
- Temporary image retention for failed or problematic recognition attempts is covered by the application's general privacy policy; no separate in-flow consent prompt is shown.
- The confirmed ingredient list is local to the device, survives app restart, is not synchronized across devices, and there is only one active confirmed list at a time.
- Later recipe recommendation functionality will consume only the locally stored confirmed ingredient list produced by this feature.
- Authentication, user profiles, direct editing of confirmed lists, recipe recommendation, recipe generation, favorites, recipe history, social features, likes, comments, follows, and user-generated recipes are outside this feature.
