---
title: Business Requirements
sidebar_position: 2
---

# Business requirements

1. The administrator must be able to **configure a house** by defining the floor plan, zones, and smart furniture hookups.
2. The platform must display an **interactive map** with real-time status of smart furniture hookups.
3. The platform must provide **real-time consumption metrics** of current utilities consumption.
4. The platform must notify users when **forecasted consumption exceeds** predefined thresholds through an automatic alert system.
5. The platform must notify users when **consumption exceeds** predefined thresholds through an automatic alert system.
6. The platform must allow **filtering of consumption** data by individual users.
7. The platform must allow managing the consumption of **individual zones**.
8. The platform must allow users to **export** both the data related to **devices** and the **map**.

## Use Cases Platform Setup
### Admin Authentication
1. **Admin Authentication (First-Time Access)**
   - **Actor:** Admin
   - **Description:**  The admin supply the credentials to access the system for the first time.
   - **Trigger:** The admin accesses the platform without an existing authentication session.
   - **Preconditions:**
     - The platform is accessed for the first time.
     - A pre-configured admin account with known credentials exists.
   - **Postconditions:**
     - The admin is successfully authenticated and can proceed with the system setup process.
   - **Main Success Scenario:**
     1. The admin navigates to the login page.
     2. The admin enters the pre-configured credentials.
     3. The admin submits the login form.
     4. The system validates the credentials.
     5. The system authenticates the user and establishes an authenticated session.
   - **Exception Scenario:**
     - **Step d:** If the user enters invalid credentials, the system displays an error message and prompts the user to re-enter the information.

2. **Logout during setup**
   - **Actor:** Admin
   - **Description:** The admin logs out of the platform during the setup process.
   - **Trigger:** The admin clicks the logout button at any point during the setup phase.
   - **Preconditions:**
     - The admin is authenticated and currently in the setup process.
   - **Postconditions:**
     - The session is terminated.
     - The admin is redirected to the login page.
   - **Main Success Scenario:**
     1. The admin clicks the "Logout" button.
     2. The system terminates the session.
     3. The system redirects the admin to the login page.

### Floor plan step
1. **Upload Floor Plan**
   - **Actor:** Admin
   - **Description:** The admin uploads a digital floor plan (SVG format) to initiate the setup process.
   - **Trigger:** The authenticated admin accesses the platform while on the “Upload Floor Plan” step of the setup.
   - **Preconditions:**
     - The admin is authenticated and in the setup process.
     - The SVG file representing the floor plan is already available in the admin’s file system.
   - **Postconditions:**
     - The floor plan is successfully stored.
   - **Main Success Scenario:**
     1. The admin accesses the "Upload Floor Plan" step page.
     2. The admin clicks the “Select file” button.
     3. The system prompts the admin to provide a digital floor plan in SVG format.
     4. The admin selects an SVG file from the local system.
     5. The system uploads and renders a preview of the file.
     6. The admin confirms the upload.
     7. The system saves the floor plan.
   - **Exception Scenario:**
     - **Step d:** If the uploaded file is not in SVG format or is invalid, the system displays an error message and prompts the admin to upload a valid file.

2. **Reupload Floor Plan**
   - **Actor:** Admin
   - **Description:** The admin reuploads a digital floor plan (SVG format), replacing the existing one.
   - **Trigger:** The admin clicks the “Upload new floor plan” button.
   - **Preconditions:**
     - The admin is authenticated and in the setup process.
     - A floor plan has already been uploaded and saved previously.
     - The new SVG file is available in the admin's local file system.
   - **Postconditions:**
     - The new floor plan replaces the previous one.
     - All existing zones and hookups associated with the previous floor plan are deleted.
   - **Main Success Scenario:**
     1. The admin accesses the "Upload Floor Plan" step page.
     2. The admin clicks the “Upload new floor plan” button.
     3. The system displays a confirmation prompt, notifying the admin that reuploading the floor plan will delete all defined zones and smart utility hookups.
     4. The admin selects an SVG file from the local system and confirms the reupload.
     5. The system uploads and renders a preview of the file.
     6. The admin confirms the upload.
     7. The system saves the floor plan.
   - **Exception Scenario:**
     - **Step 4:** If the uploaded file is not in SVG format or is invalid, the system displays an error message and prompts the admin to upload a valid file.

### Zone management
1. **Create Zone on Floor Plan**
   - **Actor:** Admin
   - **Description:** The admin creates zones by drawing polygon on the uploaded floor plan, naming each zone and customizing its color.
   - **Trigger:** The authenticated admin accesses the platform while on the “Create Zones” step of the setup.
   - **Preconditions:**
     - The admin is authenticated and in the setup process.
     - An SVG floor plan has been uploaded to the system.
   - **Postconditions:**
     - A new zone is added to the floor plan with a defined shape, name, and color.
   - **Main Success Scenario:**
     1. The admin accesses the "Create Zones" step page.
     2. The admin clicks “Add New Zone.”
     3. The system opens a zone creation form.
     4. The admin draws a polygon on the map.
     5. The admin enters a zone name.
     6. The admin clicks the “Create” button.
     7. The system validates the provided information.
     8. The system adds the zone to the floor plan.
     9. The system saves the zone and updates the floor plan.
   - **Alternative Paths:**
     - **Between Steps 4 and 7 – The admin chooses a zone color:**
       - The admin selects a different color.
       - If the polygon was already drawn, it updates with the new color.
       - Continue from Step 8.
     - **At Step 5 – The admin resizes or deletes the polygon to draw a new one:**
       - The admin adjusts or removes the current polygon.
       - Return to Step 5.
   - **Exception Scenarios:**
     - If the drawn zone overlaps with an existing one, the system displays an error and prevents confirmation until the overlap is resolved.
     - The admin discards the zone. The system returns to the "Create Zones" step page.
     - The admin skips this phase entirely.
   
2. **Edit Existing Zone**
   - **Actor:** Admin
   - **Description:** The admin, while in the “Create Zones” step of the setup, modifies a previously created zone by changing its name, shape, or color.
   - **Trigger:** The admin clicks the “Edit” button of a zone in the list of zones.
   - **Preconditions:**
     - The admin is authenticated.
     - At least one zone has already been created.
   - **Postconditions:**
     - The selected zone is updated with the new properties.
   - **Main Success Scenario:**
     1. The admin accesses the "Create Zones" step page.
     2. The admin locates the zone to be edited in the list.
     3. The admin clicks on the "Edit" button/icon associated with the zone in the list of created zones.
     4. The system opens an edit form pre-populated with the zone’s current data.
     5. The admin edits the name, color, and/or shape.
     6. The admin confirms the changes.
     7. The system updates the zone and reflects the changes on the map.
   - **Exception Scenarios:**
     - The edited shape overlaps with another zone. The system shows an error and prevents saving until the overlap is resolved.
     - The admin can discard the changes.

3. **Delete Zone**
   - **Actor:** Admin
   - **Description:** The admin, while in the “Create Zones” step of the setup, deletes an existing zone.
   - **Trigger:** The admin clicks the “Delete” button of a zone in the list of zones.
   - **Preconditions:**
     - The admin is authenticated.
     - At least one zone has already been created.
   - **Postconditions:**
     - The selected zone is removed from the system.
     - The selected zone is removed from the floor plan, and all its associated Smart Furniture Hookups are reassigned to the floor plan.
   - **Main Success Scenario:**
     1. The admin accesses the "Create Zones" step page.
     2. The admin locates the zone to be deleted in the list of zones.
     3. The admin clicks the “Delete” button/icon associated with the zone.
     4. The system displays a confirmation prompt, notifying the admin that deleting the zone will assign all Smart Furniture Hookups associated with it to the floor map.
     5. The admin confirms the deletion.
     6. The system removes the zone and all its hookups from the map.
     7. The system updates the zones list and the interactive map to reflect the deletion.
   - **Exception Scenario:**
     - The admin can discard the changes.

### Smart Furniture Hookups Management
1. **Create Smart Furniture Hookups**
   - **Actor:** Admin
   - **Description:** The admin assigns a smart furniture hookup to the floor map by providing its name and endpoint within the setup process.
   - **Trigger:** The authenticated admin accesses the platform while on the “Create Smart Furniture Hookups” step of the setup.
   - **Preconditions:**
     - The admin is authenticated and in the setup process.
     - The admin knows the endpoint of the smart furniture hookup.
   - **Postconditions:**
     - A new utility hookup is created.
   - **Main Success Scenario:**
     1. The admin accesses the "Create Smart Furniture Hookups" step page.
     2. The admin clicks the "Create New Hook-Up" button.
     3. The system opens a hookup creation form.
     4. A default icon appears on the map within the selected zone.
     5. The admin is prompted to enter the hookup’s name and endpoint.
     6. The admin adjusts the hookup’s position by dragging it.
     7. Once satisfied with the placement and details, the admin clicks the "Done" button.
     8. The system validates the data.
     9. The system saves the hookup information and updates the interactive map.
   - **Alternative Path:**
     - Step 6: If the position is within a zone, the system associates the smart furniture hookup with that zone.
   - **Exception Scenario:**
     - If the entered endpoint is not reachable or is invalid, the system displays a warning message indicating that the endpoint is unreachable.

2. **Edit Smart Furniture Hookup**
   - **Actor:** Admin
   - **Description:** The admin, while in the "Create Smart Furniture Hookups" step of the setup, modifies a previously created hookup by changing its name or endpoint.
   - **Trigger:** The admin clicks the “Edit” button of a hookup in the list of hookups.
   - **Preconditions:**
     - The admin is authenticated.
     - At least one hookup has already been created.
   - **Postconditions:**
       - The selected hookup is updated with the new properties.
   - **Main Success Scenario:**
     1. The admin accesses the "Create Smart Furniture Hookups" step page.
     2. The admin locates the hookup to be edited in the list.
     3. The admin clicks the "Edit" button/icon associated with the hookup.
     4. The system opens an edit form pre-populated with the hookup’s current data.
     5. The admin edits the name and/or endpoint.
     6. The admin confirms the changes.
     7. The system updates the hookup and reflects the changes on the map.

3. **Delete Existing Utility Hookups**
   - **Actor:** Admin
   - **Description:** The admin, while in the “Create Smart Furniture Hookups” step of the setup, deletes an existing hookup.
   - **Trigger:** The admin clicks the “Delete” button of a hookup in the list of hookups.
   - **Preconditions:**
     - The admin is authenticated.
     - At least one hookup has already been created.
   - **Postconditions:**
     - The selected hookup is removed from the system.
     - The selected hookup is removed from the floor plan.
   - **Main Success Scenario:**
     1. The admin accesses the "Create Smart Furniture Hookups" step page.
     2. The admin locates the hookup to be deleted in the list of hookups.
     3. The admin clicks the "Delete" button/icon associated with the hookup.
     4. The system displays a confirmation prompt.
     5. The admin confirms the deletion.
     6. The system removes the hookup from the floor plan.
     7. The system updates the hookups list and the interactive map to reflect the deletion.

### Set the thresholds
1. **Setup Consumption Thresholds**
   - **Actor:** Admin
   - **Description:** The admin, while in the “Setup Thresholds” step of the setup, configures alert thresholds for each utility (electricity, gas, water) by enabling a threshold for a resource and entering a numeric limit.
   - **Trigger:** The authenticated admin reaches the “Setup Thresholds” step of the setup.
   - **Preconditions:**
     - The admin is authenticated and in the setup process.
   - **Postconditions:**
     - For every resource where a threshold was enabled, a valid threshold value is saved.
   - **Main Success Scenario:**
     1. The admin accesses the "Setup Thresholds" step page.
     2. The admin toggles the threshold switch for a resource.
     3. The system enables the input field for the selected resource.
     4. The admin enters a numeric threshold.
     5. The admin clicks the "Done" button.
     6. The system validates the data.
     7. The system saves the thresholds.
   - **Alternative Path:**
     - The admin sets the threshold only for a subset of the available resources.
   - **Exception Scenario:**
     - The data provided is invalid. The system displays an error message and prevents saving.
2. **Edit Threshold Value**
   - **Actor:** Admin
   - **Description:** The admin, while in the "Setup Thresholds” step of the setup, modifies a previously configured threshold by changing its value.
   - **Trigger:** The admin clicks the input field of the resource.
   - **Preconditions:**
     - The admin is authenticated and in the setup process.
     - At least one resource has a threshold.
   - **Postconditions:**
     - The selected threshold value is updated.
   - **Main Success Scenario:**
     1. The admin accesses the "Setup Thresholds" step page.
     2. The admin locates the threshold to be edited.
     3. The admin enters a new numeric threshold.
     4. The admin clicks the "Done" button.
     5. The system validates the data.
     6. The system saves the new threshold value.
   - **Exception scenario:**
     - The data provided is invalid.

3. **Disable Threshold**
   - **Actor:** Admin
   - **Description:** The admin, while in the "Setup Thresholds” step of the setup, disables a previously configured threshold.
   - **Trigger:** The admin switches off the threshold of a resource.
   - **Preconditions:**
     - The admin is authenticated and in the setup process.
     - At least one resource has a threshold.
   - **Postconditions:**
     - The selected threshold value is removed.
   - **Main Success Scenario:**
     1. The admin accesses the "Setup Thresholds" step page.
     2. The admin locates the threshold to be disabled.
     3. The admin switches off the threshold of a resource.
     4. The admin clicks the "Done" button.
     5. The system validates the data.
     6. The system removes the threshold.
   - **Exception scenario:**
     - The data provided is invalid.

### Management of the household users
1. **Add New Household Users**
   - **Actor:** Admin
   - **Description:** The admin, while in the "Create Household Users” step of the setup, creates a household user account by providing a name, password, and a token to be recognized by the smart furniture hookups.
   - **Trigger:** The authenticated admin accesses the platform while on the “Create Household Users" step of the setup.
   - **Preconditions:**
     - The admin is authenticated and in the setup process.
     - The admin knows the household user's username.
   - **Postconditions:**
     - A new user account is created.
   - **Main Success Scenario:**
     1. The admin accesses the "Create Household Users" step during setup.
     2. The admin clicks “Add New User.”
     3. The system opens a household user creation form.
     4. The admin is prompted to enter the username and password.
     5. The system validates the data.
     6. The system creates a new user.
   - **Exception scenarios:**
     - If the entered name already exists, the system displays an error message requesting a different username.
     - If required fields are missing or invalid, the system prompts the admin to correct the details before proceeding.

2. **Edit User**
   - **Actor:** Admin
   - **Description:** The admin, while in the "Create Users” step of the setup, modifies a previously created user by changing its name, password, or token.
   - **Trigger:** The admin clicks the “Edit” button of a user in the list of users.
   - **Preconditions:**
     - The admin is authenticated and in the setup process.
     - At least one user has already been created.
   - **Postconditions:**
     - The selected user is updated with the new properties.
   - **Main Success Scenario:**
     1. The admin accesses the "Create Users" step during setup.
     2. The admin locates the user to be edited in the list.
     3. The admin clicks on the "Edit" button/icon associated with that user.
     4. The system opens an edit form pre-populated with the user’s current details.
     5. The admin updates the user’s name and/or password.
     6. The admin submits the changes by clicking the “Done" button.
     7. The system validates the updated information.
     8. The system updates the user’s details and updates the user list to reflect the edit.
   - **Exception scenarios:**
     - If the entered name already exists, the system displays an error message requesting a different username.
     - If required fields are missing or invalid, the system prompts the admin to correct the details before proceeding.

3. **Delete User**
   - **Actor:** Admin
   - **Description:** The admin, while in the "Create Users” step of the setup, deletes an existing user.
   - **Trigger:** The admin clicks the “Delete” button of a user in the list of users.
   - **Preconditions:**
     - The admin is authenticated and in the setup process.
     - At least one user has already been created.
   - **Postconditions:**
     - The selected user account is removed from the system.
     - The user no longer appears in the list.
   - **Main Success Scenario:**
     1. The admin accesses the "Create Users" step during setup.
     2. The admin locates the user to be deleted in the list.
     3. The admin clicks on the "Delete" button/icon associated with that user.
     4. The system prompts the admin for confirmation.
     5. The admin confirms the deletion.
     6. The system validates the confirmation and deletes the user account.
     7. The system updates the user list to reflect the removal.
   - **Exception scenario:**
     - The admin cancels the deletion prompt and no changes are made.

## Settings Use Cases
### Management of Admin Account
1. **Edit Password**
   - **Actor:** Admin
   - **Description:** The admin, while on the "Account” page, modifies their own password.
   - **Trigger:** The admin clicks the “Change Password” button.
   - **Preconditions:**
     - The admin is authenticated.
   - **Postconditions:**
     - The admin's password is updated.
   - **Main Success Scenario:**
     1. The admin accesses the "Account” page.
     2. The admin clicks on the “Change Password” button.
     3. The system opens an edit form.
     4. The admin is prompted to enter the new password.
     5. The system validates the data.
     6. The system updates the admin's password.
   - **Exception scenario:**
     - If required fields are missing or invalid, the system prompts the admin to correct the details before proceeding.
2. **Reset Password**
   - **Actor:** Admin
   - **Description:** The admin resets their password using a reset code.
   - **Trigger:** The admin clicks the “Reset Password” button.
   - **Preconditions:**
     - The admin knows the reset password code.
   - **Postconditions:**
     - The admin's password is updated.
   - **Main Success Scenario:**
     1. The admin accesses the "Reset Password” page.
     2. The admin is prompted to enter the new password and the reset code.
     3. The system validates the data.
     4. The system updates the admin's password.
   - **Exception scenario:**
     - If required fields are missing or invalid, the system prompts the admin to correct the details before proceeding.

### Export
1. **Export the Map**
   - **Actor:** User
   - **Description:** The user, while in the “Export” section of the Settings page, exports the current map by downloading it to their local system.
   - **Trigger:** The user clicks the “Export Map” button in the “Export” section of the Settings page.
   - **Preconditions:**
     - The user is authenticated.
   - **Postconditions:**
     - The map is downloaded to the user’s local system.
   - **Main Success Scenario:**
     1. The user accesses the “Export” section of the Settings page.
     2. The user clicks the “Export Map” button.
     3. The system prompts the user to download the generated SVG file.
     4. The user downloads the SVG file.

2. **Export the Smart Furniture Hookups**
   - **Actor:** User
   - **Description:** The user, while in the “Export” section of the Settings page, exports the current hookups data by downloading them to their local system.
   - **Trigger:** The user clicks the “Export Smart Furniture Hookups Details” button in the “Export” section of the Settings page.
   - **Preconditions:**
     - The user is authenticated.
     - At least one smart furniture hookup is created.
   - **Postconditions:**
     - A JSON file containing an array of all smart hookup objects is downloaded to the user’s local system.
   - **Main Success Scenario:**
     1. The user accesses the “Export” section of the Settings page.
     2. The user clicks the “Export Smart Furniture Hookups Details” button.
     3. The system serializes the records into a structured JSON format.
     4. The system prompts the user to download the generated JSON file.
     5. The user downloads the JSON file.

## Dashboard Use Cases
### Filter
1. **Filter Utility Consumptions**
   - **Actor:** User
   - **Description:** The user filters utility consumption data by selecting a specific household user.
   - **Trigger:** The user clicks the “Filter” button and chooses a household user.
   - **Preconditions:**
     - The user is authenticated.
     - At least one household user has already been created.
   - **Postconditions:**
     - The utility consumption display is limited to the data belonging to the selected household user.
   - **Main Success Scenario:**
     1. The user accesses a page that displays utility consumption data.
     2. The user clicks the “Filter” button.
     3. The system opens a list of household users.
     4. The user locates and clicks on the desired household user.
     5. The system applies the filter and refreshes the display to show only that household user’s consumption data.
