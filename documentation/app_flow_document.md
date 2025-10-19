# App Flow Document for shootmagic-image-workflow

## Onboarding and Sign-In/Sign-Up

When a user opens the application URL in their browser, they arrive on a simple landing page that introduces the image processing tool. There is no sign-up or sign-in required because this app is entirely client-side and does not store personal credentials on a backend. The landing page displays a prominent “Get Started” button that takes the user directly into the main dashboard. If the user wishes to return later, they can bookmark the page or simply revisit the application link. There are no password-recovery or social login flows, since no authentication is present in this version of the app.

## Main Dashboard or Home Page

Clicking the “Get Started” button brings the user to the dashboard, which fills the entire browser window. A top header bar displays the application title on the left and a settings icon on the right. Below the header, the central area is divided into two sections. The left section contains the upload interface and preset selector. The right section shows a responsive grid of previously processed image pairs loaded from browser storage. Each pair is displayed in a card with the original image on top and the transformed image below. Hovering over a card reveals a small button to copy the transformed image URL to the clipboard.

## Detailed Feature Flows and Page Transitions

To begin processing, the user either drags and drops image files onto the upload area or clicks the upload box to open the file chooser. As soon as one or more files are selected, thumbnails appear in the upload area with an icon allowing the user to remove any unwanted files. The user then clicks the preset dropdown to choose a transformation style, such as “Amazon Catalog.” Once the images and preset are in place, the user activates the processing flow by clicking the “Process Images” button below the upload area. At that moment, each image enters a processing queue. The dashboard state updates to show a spinner overlay on each thumbnail indicating work in progress.

Behind the scenes, the application converts each image file to a Base64 string and sends a POST request to the external Chutes.ai API, attaching the user’s API key from the environment variable. Each request resolves independently. When an API call succeeds, the dashboard replaces the spinner with the returned image URL, displays the new image in place of the original thumbnail, and persists the input/output pair into localStorage. If multiple images were queued, this cycle repeats until all have results. Once the batch completes, the grid on the right side of the screen updates to include the new cards showing before-and-after pairs alongside previously processed pairs. Users can scroll through the grid, and if they wish to process more images, they can scroll up or click the upload area without leaving the dashboard.

If the user clicks the settings icon in the header, a modal overlay appears. This modal allows the user to set a default preset for future sessions and clear all stored results from localStorage. After saving preferences or clearing data, the modal closes and the dashboard reloads its grid to reflect any changes.

## Settings and Account Management

Since there is no user account, settings focus exclusively on application preferences and storage control. Opening the settings modal reveals a dropdown to choose a default image transformation preset. Changing the default causes subsequent uploads to preselect that style automatically. The modal also offers a button to clear all processed image data from localStorage, resetting the dashboard grid to empty. Toggling preferences and clearing data happen instantly when the user confirms the action. Closing the modal returns the user directly to the dashboard without any page reload.

## Error States and Alternate Paths

If the user tries to upload a file that is not a supported image type, the upload component highlights the invalid file and displays an inline error message beneath the upload area. The user can remove the bad file and continue. During processing, if any API call fails due to a network issue, missing API key, or server error, the spinner on that image turns into an error icon. Hovering over the icon shows a tooltip with the error message. The user can click a retry icon to resend the failed image to the API. If localStorage is full or unavailable, a banner appears at the top of the dashboard advising the user that results cannot be saved and suggests freeing up browser space or using a different device. When connectivity is restored or the user corrects the issue, they can retry processing or refreshing the page to resume normal operation.

## Conclusion and Overall App Journey

The user’s journey begins with a quick landing page and a single click to start. On the dashboard, they select images, choose a preset, and trigger the processing flow. While each image is processed by the external API, the interface presents clear loading indicators and updates in real time. Successful results appear side by side with input images and persist across sessions in the localStorage-backed grid. Settings are easily accessible via a modal for default presets and data management. Error feedback and retry options ensure that the user can recover from problems without disrupting the overall experience. The typical end goal of producing a clean transformation for each image is achieved in just a few straightforward steps, creating a seamless and user-friendly workflow from start to finish.