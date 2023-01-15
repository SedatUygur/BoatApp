Use Case
Over the past few years, HowWeRoll Rentals, the world’s largest recreational vehicle (RV) rental company, has dominated the RV rental marketplace. Its tagline is “We have great service because that’s How We Roll!” Its rental fleet includes every style of camper vehicle, from palatial mobile homes to old-school, chrome Airstream campers. If you’re plagued with wanderlust, HowWeRoll has the cure!

As the lead Salesforce developer for HowWeRoll, you’ve been instrumental in making the company a huge success. In order to continue revenue growth, the company’s leadership has decided to expand beyond its core RV market and enter the recreational boating industry, as surveys have shown that a large share of RV travelers are also boat owners. Instead of investing in boats of its own, HowWeRoll plans to start a boat-sharing program where the company acts as a leasing agent for its customers’ boats. HowWeRoll is calling this new service Friend Ships.

Tatiana Loyal is the stellar Salesforce developer at HowWeRoll. She’s started an implementation of a custom Lightning interface, surfaced in Lightning Experience, but she’s now out on maternity leave. You’ve been asked to continue her work developing a Lightning application that enables sales associates to enter information about their customers’ boats, including boat’s locations. You’ve also been asked to enable your team members to post comments and ratings about their experiences when they inspect each boat.

Rather than start over, you begin with some code written by Tatiana. As you know—good programmers write good code; great programmers reuse good code.

First you develop a custom search engine so HowWeRoll’s sales associates can dynamically filter the boats based on boat type (such as a fishing boat, pleasure boat, party boat) in order to match customer requests with the boating inventory.

Then you create a map that displays up to 10 boats based on the current user’s location.

Finally, you convert an existing, outdated Visualforce page that shows boats similar to the search query into a reusable solution with Lightning Web Components.

Key Stakeholders and Team
Solange Pereira (CEO)
Weimar Williams (Salesforce admin)
Byanca Gowda (Salesforce architect)
Renata Pan (UX engineer)
Tatiana Loyal (Salesforce developer)
Standard Objects
You’ll work with the following standard objects.

Contact—Organization contacts and boat owners.
User—The people posting boat reviews and comments.
Custom Objects
You’ll work with the following custom objects.

Boat—Information about the boats that are owned by your contacts. This object contains a geolocation field so that you can plot its typical dock location on a map. This object has a master-detail relationship with the Contact standard object and a lookup relationship with BoatType.
Boat Type—A list of the different types of boats, such as fishing boat, powerboat, sailboat, or party barge.
BoatReview—Comments on and ratings of boats. This object has a master-detail relationship with Boat. So one boat can have many comments/ratings.
Entity Diagram
From Setup, enter Schema Builder in the Quick Find box, then select Schema Builder to view an interactive version of the image. For more information, see the Work with Schema Builder unit in the Data Modeling module.

Entity diagram. Boat Type object linked to the Boat object (Field Boat Type, Lookup); BoatReview linked to Boat (Field Boat, Master-Detail); User linked to Boat (CreatedBy, lookup; LastModifiedBy, lookup); Contact linked to Boat (Field Contact, Master-Detail)

Application Design
The time you spent meeting with key HowWeRoll stakeholders was worthwhile; the team came up with the following blueprint for the Lightning page. First, the Gallery:

Friend Ships main page, showing the search form on the top, the Gallery on the bottom left of the page, record details on the top right of the page, Current Boat Location right below it, on the bottom right side of the page. The active tab is the Gallery.

Here is the Boats Near Me tab view. The map on the left shows a list of boats based on the current user’s location. The map on the right shows the currently selected boat's location. You will also reuse the boatMap component on the Boat record's page.

Friend Ships main page, showing the Boats Near Me tab, with 10 map marks distributed across the North America map, showing maps in Mexico, the United States and Canada. The active tab is Boats Near Me.

Then the Boat Editor tab, a Lightning data table lets you edit multiple records at once.

Friend Ships main page, showing the Boat Editor tab, with a list of boats, filtered by Type, where the user can edit several records at once. The active tab is the Boat Editor.

You also need to build a component for the Boat Reviews and Rating System, as shown.

Two screenshots, side by side. On the left, the Add Review component, showing a review ready to be saved. On the right, the Reviews tab, showing the details about the review after it was saved, in read-only mode.

After assessing the task ahead, you guzzle a cup of coffee, or your preferred beverage, and decide that the best way to conquer this project is to break it into smaller pieces. You plan out the following phases which, when completed, result in a solid finished product.

Build the Boat Message Service Channel
In order to create communication across the Document Object Model (DOM), you create a Lightning Message Service channel. As an informed Salesforce developer, you know that a Lightning web component uses a Lightning Message Service channel to access the Lightning Message Service API. Use the following settings for your message channel.

Description = This is a sample Lightning Message Channel for the Lightning Web Components Superbadge.
Make sure the channel is exposed.
Create the <lightningMessageFields> for the recordId (boat’s record Id) field.
MasterLabel = BoatMessageChannel
Deploy the message channel (below) to your org so you can reference it in Lightning web components. Import it from the new @salesforce/messageChannel scoped module and include the functions, context, and scope you’re interested in.

<?xml version="1.0" encoding="UTF-8"?>
<LightningMessageChannel xmlns="http://soap.sforce.com/2006/04/metadata">
    <description>This is a sample Lightning Message Channel for the Lightning Web Components Superbadge.</description>
    <isExposed>true</isExposed>
    <lightningMessageFields>
        <description>This is the record Id that changed</description>
        <fieldName>recordId</fieldName>
    </lightningMessageFields>
    <masterLabel>BoatMessageChannel</masterLabel>
</LightningMessageChannel>
Use BOATMC for the name of your imported message channel inside the JavaScript files.

// imports 
// { functions, context, scope } 
import { ..., ..., ... } from 'lightning/messageService';
import BOATMC from '@salesforce/...';
Let’s Sail Away!
The boat is safer anchored at the port, but that’s not the aim of boats. Let’s sail away! A journey of a million nautical miles begins with a single line of code, right?

Get your motor running by displaying a form with a dropdown that lists each boat type, along with a New Boat button.

Main screen, divided into five different sections. 1. Find a Boat. 2. Search Results. 3. Boat Tile. 4. Record details plus reviews. 5. Current boat location.

Figure: Building the components. Main screen, divided into five different sections. 1. Find a Boat. 2. Search Results. 3. Boat Tile. 4. Record details and boat reviews. 5. Current boat location.

Figure: Building the Search Results. The first tab shows the Gallery. The second tab shows the Boat Editor. The third tab shows the Boats Near Me.

Figure: Building the Search Results. The first tab shows the Gallery. The second tab shows the Boat Editor. The third tab shows the Boats Near Me.

Figure: Building the Record details and boat reviews. The first tab shows the Details. The second tab shows the a message if there are no reviews available, or a list of reviews for a given boat. The third tab shows the Add Review.

Figure: Building the Record details and boat reviews. The first tab shows the Details. The second tab shows the a message if there are no reviews available, or a list of reviews for a given boat. The third tab shows the Add Review.

To build the form and the page, create all the required components. Each section is highlighted with its corresponding number in the figures above, and they can contain more than one component. Ready to dive deep? You can do this!

Friend Ships Lightning Page
First, create a Lightning app page named Friend Ships that uses the Main Region and Right Sidebar layout. Put the component boatSearch in the main region and activate the page. In this project you won’t need to add this Lightning app page to any existing app or mobile navigation.

Note
Note
Create and test the components in this new Lightning page. Although each challenge step will evaluate different component's code, we will only assess if the entire Friend Ships Lightning page meets the requirements in the 'Build the Friend Ships Lightning Page with all the components' challenge.

Then, in the App Manager, create a new Lightning app named Friend Ships (API name: Friend_Ships) that’s directly accessible via its URL. Use standard navigation style with support to desktop and mobile. Add the Friend Ships page to the app as a Navigation Item.

Lastly, grant access to the following profiles: System Administrator and Standard User.

This configuration must result in the creation of the following objects.

Object Type	Object Label
Lightning App	Friend Ships
Lightning Page	Friend Ships
Custom Tab Definition	Friend Ships
Build the Search Form and Add It to a New Lightning Page
The boatSearch component is a container component that invokes both the boatSearchForm and boatSearchResults. It contains a <lightning-layout> with multiple rows, including a <lightning-layout-item> with size equals to 12 on the top, a <lightning-card> with the title "Find a Boat", and another <lightning-layout-item> with size equals to 12 on the bottom.

Include a standard <lightning-spinner> in the boatSearch, using alternative-text = "Loading". The spinner must match the Salesforce Lightning Design System (SLDS) brand color.

Show the spinner while the form is loading the boats. Use the attribute isLoading via the two following methods: handleLoading() and handleDoneLoading().

The component boatSearch must also have a button to create a new boat. To keep the design simple to use and maintain, Byanca requested you use a <lightning-button> inside a slot called actions. Label it New Boat and invoke a function named createNewBoat() that uses the NavigationMixin extension to open a standard form so the user can create a new boat record.

Markup (boatSearch.html)
<template>
  <lightning-layout multiple-rows>
    <!-- Top -->
    <lightning-layout-item size="12">
      <lightning-card title="Find a Boat">
        <!-- New Boat button goes here -->
        <p class="slds-var-p-horizontal_small">
          <!-- boatSearchForm component goes here -->
        </p>
      </lightning-card>
    </lightning-layout-item>
    <!-- Bottom -->
    <lightning-layout-item size="12" class="slds-p-top_small slds-is-relative">
      <!-- Spinner goes here -->
      <!-- boatSearchResults goes here -->
      <!-- onloading={handleLoading} ondoneloading={handleDoneLoading} -->
    </lightning-layout-item>
  </lightning-layout>
</template>
JavaScript file (boatSearch.js)
Make sure to use the correct decorators for the attributes.

 // imports
export default class BoatSearch extends LightningElement {
  isLoading = false;
  
  // Handles loading event
  handleLoading() { }
  
  // Handles done loading event
  handleDoneLoading() { }
  
  // Handles search boat event
  // This custom event comes from the form
  searchBoats(event) { }
  
  createNewBoat() { }
}
Boat Search Form
The boatSearchForm is a component that lets users filter results by boat type using a <lightning-combobox> menu. The menu is middle-aligned, with text left-aligned and uses standard SLDS styling. An empty string is both the first and default value. Label this first value All Types.

Component boatSearchForm, with the title equal to Find a Boat, a dropdown box in the middle for the boat types, and a button on the upper right corner of the component, with the label equal to New Boat. The button is white, and the text is blue.

According to Byanca Gowda, the Salesforce architect at HowWeRoll, the boat types can be retrieved through an Apex method called getBoatTypes(), from the BoatDataService class. It’s important to fix the declaration of some methods in this class so they can be referenced by Lightning web components.

Show these values in a <lightning-combobox> that uses the selectedBoatTypeId for the value being displayed, and the values in the property searchOptions for the available options.

Changing the value of this dropdown menu must dynamically trigger the search for the boats and display the results in the boatSearchResults component. You need to fire a custom event named search, using the method handleSearchOptionChange(event) to pass the value of selectedBoatTypeId in the detail using the key boatTypeId through a dispatched event.

Complete the boatTypes function’s logic to populate the map, returning the types names and Ids.

The newly created custom event search must cause the application to query for the boats.

Markup (boatSearchForm.html)
<template>
  <lightning-layout>
    <lightning-layout-item class="slds-align-middle">
      <lightning-combobox class="slds-align-middle"></lightning-combobox>
    </lightning-layout-item>
  </lightning-layout>
</template>
JavaScript file (boatSearchForm.js)
// imports
// import getBoatTypes from the BoatDataService => getBoatTypes method';
export default class BoatSearchForm extends LightningElement {
  selectedBoatTypeId = '';
  
  // Private
  error = undefined;
  
  searchOptions;
  
  // Wire a custom Apex method
    boatTypes({ error, data }) {
    if (data) {
      this.searchOptions = data.map(type => {
        // TODO: complete the logic
      });
      this.searchOptions.unshift({ label: 'All Types', value: '' });
    } else if (error) {
      this.searchOptions = undefined;
      this.error = error;
    }
  }
  
  // Fires event that the search option has changed.
  // passes boatTypeId (value of this.selectedBoatTypeId) in the detail
  handleSearchOptionChange(event) {
    // Create the const searchEvent
    // searchEvent must be the new custom event search
    searchEvent;
    this.dispatchEvent(searchEvent);
  }
}
Next, begin displaying filtered and unfiltered search results in a responsive layout.

Populate the Search Results and Search Filter
It’s time to start showing off pictures of our boats. By the end of this phase, your page will display a gallery of the boats in the HowWeRoll inventory, optionally filtered by boat type.

boatSearchResults is the container component for all the boats in the Gallery, Boats Near Me, and Boat Editor sections. Recall that Boat Editor is a Lightning DataTable used to edit several records at once. You build it during this project.

The image shows the form, with the select boat type being Jet Ski, and the gallery showing four tiles, each one with a different jet ski record. The first tile on the gallery is selected. The active tab is Gallery.

This component lets the user perform different actions, so make sure it does the following.

Refreshes itself and other components after a record operation happens
Displays important messages in toast notifications
The search for boat records must be initiated by a function named searchBoats(event) in the boatSearch component. Customize this function to pass the value of boatTypeId to the public function searchBoats(boatTypeId) from the boatSearchResults component, so it can be used by getBoats().

You need to use an Apex class, BoatDataService, to get the search results. This class uses a method, getBoats(), that takes a boatTypeId of type String and returns a list of boats filtered by that Id. When a user selects All Types, the empty string is passed to this function so it returns all boats from all types.

In this component, you need to dispatch a custom event called either loading or doneloading when searching for the boats. Make sure to use the isLoading private property to dispatch the event only when needed.

But first things first—let’s make sure we have a component for the boat tile, handling events and showing the correct data.

Build the boatTile Component
Add the required code to the Lightning web component boatTile to display a boat for rent with a <div> that reacts to a click event using the function selectBoat(). It must change its own class between tile-wrapper selected and tile-wrapper (see the CSS section below) using the function tileClass(), depending on the value of selectedBoatId. Make sure you implement best practices and store these strings in constants called TILE_WRAPPER_SELECTED_CLASS and TILE_WRAPPER_UNSELECTED_CLASS respectively.

This state must adhere to the selected boat across the components. So make sure to add the required logic into selectBoat() to send the correct detail information, assigning boat.Id to boatId and then adding it to a custom event named boatselect, so the boatSearchResults component can propagate the event using the message service. Make sure you are wiring the messageContext in the boatSearchResults component in order to publish the message.

For this reason, the JavaScript file requires two different attributes to receive information about the boat that displays in the tile (boat) and the currently selected boat Id (selectedBoatId). Make sure to use the correct decorators for these attributes.

The boat image must be set as the background of a <div> with a class equals to tile and it must be retrieved via a function named backgroundStyle(). The return of this function must be a string that contains the background-image:url() function, showing the boat picture from the field Picture__c on the Boat__c object.

Add the boat name and boat owner to the bottom of the tile, inside a <div> using lower-third as the class for this <div>. Your work must look like the design provided by Renata Pan as seen in all the images present in this project.

The boat name must be inside an <h1> tag using the slds-truncate and slds-text-heading_medium classes.
The boat’s owner’s name must be added to an <h2> tag that uses the slds-truncate and slds-text-heading_small classes.
The boat price must use a Lightning formatted number, with a maximum of two fraction digits, in a <div> that uses the slds-text-body_small class.
The boat length must be in a <div> that uses the slds-text-body_small class.
The boat type must be in a <div> that uses the slds-text-body_small class.
Markup (boatTile.html)
<template>
  <div onclick={selectBoat} class={tileClass}>
    <div style={backgroundStyle} class="tile"></div>
    <div class="lower-third">
      <!-- Boat information goes here -->
    </div>
  </div>
</template>
JavaScript file (boatTile.js)
// imports
export default class BoatTile extends LightningElement {
  boat;
  selectedBoatId;
  
  // Getter for dynamically setting the background image for the picture
  get backgroundStyle() { }
  
  // Getter for dynamically setting the tile class based on whether the
  // current boat is selected
  get tileClass() { }
  
  // Fires event with the Id of the boat that has been selected.
  selectBoat() { }
}
CSS (boatTile.css)
.tile {
  width: 100%;
  height: 220px;
  padding: 1px !important;
  background-position: center;
  background-size: cover;
  border-radius: 5px;
}
.selected {
  border: 3px solid rgb(0, 95, 178);
  border-radius: 5px;
}
.tile-wrapper {
  cursor: pointer;
  padding: 5px;
}
Build the Gallery
The component boatSearchResults gets the data returned by getBoats(), which stores the search results in a component attribute boats through a wired function called wiredBoats(). Next, boatSearchResults loops through the results and displays each one as a boatTile, arranged in a responsive grid. Use a scoped <lightning-tabset> that’s only rendered if the attribute boats contains data to be displayed.

Gallery with 8 boat records being displayed. The active tab is the Gallery.

Use a <lightning-tab> labeled Gallery to display the boat tiles. Renata requested this gallery be scrollable, so create a <div> using the slds-scrollable_y class. Inside this <div>, create a <lightning-layout> that is horizontally aligned to the center, and allows multiple rows.

The component boatSearchResults loops through data returned by an Apex method and generates boatTile components in the gallery. Now you need a way to loop through the boats data, showing each boat as a boat tile. In order to achieve that, create a <template>. Do the following for each item named boat inside the boats.data:

Display a <lightning-layout-item> using the boat Id as the key.
Since Renata requested that the layout be responsive, apply the following properties to it:
Property	Value
Padding	"around-small"
Standard size	"12"
Size on small devices	"6"
Size on medium devices	"4"
Size on large devices	"3"
This <lightning-layout-item> also contains each boat tile, passing both the currently selected boat Id and the information about the boat in each iteration, by using the custom event named boatselect created in the boatTile component.

The info about the currently selected boat must be sent to other components, like the Current Boat Location and Details. In the boatSearchResults component, use the function updateSelectedTile() to update the information about the currently selected boat Id based on the event.

Don’t forget that this component must also use the existing loading spinner when loading records.

Boat Editor–Edit Boats En Masse!
Byanca, Renata, and the engineering team came up with a solution where they can edit boat records in bulk. They need your help implementing this solution. It looks like this:

Boat Editor in edit mode, with several changes in different boat records, ready to be saved. The changed values are highlighted with a yellow background. Unchanged values have a white background. The two available buttons are Cancel and Save. The active tab is the Boat Editor.

There is a Boat Editor tab on the boatSearchResults component that displays a scrollable table (using a <div> with the same settings used for the gallery) with inline editing enabled. Here the user can edit the boat’s Name, Length, Price, and Description fields.

Add a new <lightning-tab> for the Boat Editor, using Boat Editor as the label. Inside this tab, use a <lightning-datatable> to implement this functionality and bind to the same boat’s data that’s being displayed on the gallery. Retrieve the columns and data. Use the function handleSave() to save all the records using the updateBoatList(Object data) method from the Apex class BoatDataService. Hide the checkbox column.

When saving changes successfully, the data table must be refreshed asynchronously (async), and the following success toast notification must be displayed, using the constants SUCCESS_VARIANT, SUCCESS_TITLE and MESSAGE_SHIP_IT:

Title: Success
Message: Ship It!
In case there’s an error, catch and show the error message in an error toast notification, using the constants CONST_ERROR as the event title and ERROR_VARIANT as the variant.

On success, call the function refresh(), that must trigger the loading spinner, invoke refreshApex() to refresh a wired property and then stop the spinner.

Boats Near Me
For this tab, add a new <lightning-tab> for Boats Near Me tab, using Boats Near Me as the label. When adding the component boatsNearMe, keep in mind this component uses the method getBoatsByLocation() from the BoatDataService class. For this reason, pass the current boat type (boatTypeId) so the nearby boats are also filtered down.

We check the requirements and snippets that are specific to boatsNearMe in a minute. For now, here is the code to use for the component boatSearchResults.

Markup (boatSearchResults.html)
<template>
  <lightning-tabset ...>
    <lightning-tab label="Gallery">
      <div class="slds-scrollable_y">
        <!-- layout horizontally aligned to the center  -->
        <!-- layout allowing multiple rows -->
        <lightning-layout ...>
          <!-- template looping through each boat -->
          <template ...>
            <!-- lightning-layout-item for each boat -->
            <lightning-layout-item ...>
               <!-- Each BoatTile goes here -->
            </lightning-layout-item>
          </template>
        </lightning-layout>
      </div>
    </lightning-tab>
    <lightning-tab label="Boat Editor">
      <!-- Scrollable div and lightning datatable go here -->
    </lightning-tab>
     <lightning-tab label="Boats Near Me">
      <!-- boatsNearMe component goes here -->
    </lightning-tab>
  </lightning-tabset>
</template>
JavaScript file (boatSearchResults.js)
// ...
const SUCCESS_TITLE = 'Success';
const MESSAGE_SHIP_IT     = 'Ship it!';
const SUCCESS_VARIANT     = 'success';
const ERROR_TITLE   = 'Error';
const ERROR_VARIANT = 'error';
export default class BoatSearchResults extends LightningElement {
  selectedBoatId;
  columns = [];
  boatTypeId = '';
  boats;
  isLoading = false;
  
  // wired message context
  messageContext;
  // wired getBoats method 
  wiredBoats(result) { }
  
  // public function that updates the existing boatTypeId property
  // uses notifyLoading
  searchBoats(boatTypeId) { }
  
  // this public function must refresh the boats asynchronously
  // uses notifyLoading
  refresh() { }
  
  // this function must update selectedBoatId and call sendMessageService
  updateSelectedTile() { }
  
  // Publishes the selected boat Id on the BoatMC.
  sendMessageService(boatId) { 
    // explicitly pass boatId to the parameter recordId
  }
  
  // The handleSave method must save the changes in the Boat Editor
  // passing the updated fields from draftValues to the 
  // Apex method updateBoatList(Object data).
  // Show a toast message with the title
  // clear lightning-datatable draft values
  handleSave(event) {
    // notify loading
    const updatedFields = event.detail.draftValues;
    // Update the records via Apex
    updateBoatList({data: updatedFields})
    .then(() => {})
    .catch(error => {})
    .finally(() => {});
  }
  // Check the current value of isLoading before dispatching the doneloading or loading custom event
  notifyLoading(isLoading) { }
}
Note
Note
Previously, in order to enable inline editing for multiple rows, a developer could use the updateRecord method from uiRecordApi, with Promise.all(promises) causing each record update to occur in its own transaction. We're now using an approach with Apex in a pattern that is more scalable. More details in the documentation.

Show the Boats on a Map
Once they sign a lease, HowWeRoll clients need to know where to pick up the boats they’re leasing. For this task, deploy a mapping component to show the user where the boat docks. Also update the component to use the BoatMessageChannel__c titled BOATMC and events dispatched from anywhere in the application.

The component boatMap being displayed, showing the location of the currently selected boat. The boat is located on the bottom right side of the screen. The component title is Current Boat Location.

The component boatMap and its JavaScript file were included in the unlocked package that you installed as part of the prework for this project, so you will be able to find it in your org. You only need to make a few adjustments.

To retrieve the items from the unlocked package, use the Salesforce DX command force:source:retrieve or explicitly declare them in your package.xml file to use the feature Retrieve Source in Manifest from Org.

Before you begin, review each of the files that came with the component to get a feel for how they work. At this point you’re probably familiar with the event boatselect being fired once the user clicks a boatTile. Remember that while the event is fired when a user clicks a boat from the boatTile component, other components that have instances of boatTile use different methods for this event’s listener.

Make sure to subscribe to the events inside the connectedCallback() to be able to retrieve the boat recordId. Make sure you are wiring the messageContext in order to subscribe to the message channel. Ensure that every time the value of recordId changes, it uses the getRecord method from uiRecordApi to retrieve the values from the fields Geolocation__Longitude__s and Geolocation__Latitude__s. Then populate the location in the mapMarkers property using the function named updateMap().

Make these adjustments and then add the component boatMap to the right sidebar in the Lightning page.

The boatMap component is wrapping the <lightning-map> in a <lightning-card> with title Current Boat Location and setting the zoom level equal to 10, to keep the UI consistent with the other elements on the page.

Now that we have a working map component showing the boat’s location, it’s time to show the boats near you!

Display Nearby Boats!
We know that HowWeRoll Rentals has users around the globe. Renata and Byanca want a solution that shows boats near the user using the browser location and boat type to display up to 10 boats on a map. Browser location is only used with the user’s consent while the page is open.

Here’s an example of how the results vary depending on the user’s location. For a user in South America:

Boats Near Me displaying a map with ten map markers across South America. On the right side of the map, there is a list of all the markers, with the geolocation (latitude, longitude), and the boat’s name, including the current user’s location. The active tab is Boats Near Me.

For a user in China or India:

Boats Near Me displaying a map with ten map markers across Asia. On the right side of the map, there is a list of all the markers, with the geolocation (latitude, longitude), and the boat’s name, including the current user’s location. The active tab is Boats Near Me.

Well, you get the gist of it. Let’s think about it for a second. The implementation happens in three steps.

Get the user’s location from the browser, if the user consents.
Use the browser’s location (latitude and longitude), plus the currently selected boat type, to load the boats using a method from the BoatDataService class.
Create a list of map markers for the <lightning-map> using the returned values from the Apex class.
Let’s begin—create the boatsNearMe component. Create a <lightning-map> inside a <lightning-card> with a relative position, using standard Salesforce Lightning Design System style.

Get the User’s Location from the Browser
Complete the method named getLocationFromBrowser(). This method has to leverage the browser API to get the current position using the function getCurrentPosition(), saving the coordinates into the properties latitude and longitude using the arrow notation: position => {}. To follow performance best practices, add logic to renderedCallback() to get the location from the browser only if the map has not been rendered yet. Use the property isRendered.

Get the Boats by Location
Now that we have the user’s location stored in the properties, invoke the method BoatDataService.getBoatsByLocation(), passing the coordinates and the boat type Id. Use the wiredBoatsJSON({error, data}) function to handle the result from the wired getBoatsByLocation().

If the result contains any data, invoke the function createMapMarkers(), passing the data as a parameter.
If an error occurred, show the error message in a toast error event (use the constant ERROR_VARIANT), with the title equals to the constant ERROR_TITLE.
Create the Map Markers and Display the Boats on the Map
To show the map, add a <lightning-map> that uses the property mapMarkers from the JavaScript file, as the component’s map markers. This property is populated by a function named createMapMarkers(boatData), using the following logic.

The first marker in mapMarkers must have the current user’s latitude and longitude. Use the constants LABEL_YOU_ARE_HERE for the title, and ICON_STANDARD_USER for the icon.
The other marks on that list must be generated from the boatData parameter. For each marker, the title must be the boat name, and the marker must have the boat’s latitude and longitude.
For the HTML portion, add a <lightning-spinner> inside a <template>, using Loading for the alternative-text and brand for the variant. Check the property isLoading to define if it must be displayed or not. The map must display the spinner immediately, and it must hide it after the wired boats get loaded and the map markers are created, or in the case of some error occurs.

Finally, invoke a <lightning-map> passing the mapMarkers, placing it right below the <lightning-spinner>.

Markup (boatsNearMe.html)
<template>
  <lightning-card class="slds-is-relative">
     <!-- The template and lightning-spinner goes here -->
     <!-- The lightning-map goes here -->
     <div slot="footer">Top 10 Only!</div>
  </lightning-card>
</template>
JavaScript file (boatsNearMe.js)

// imports
const LABEL_YOU_ARE_HERE = 'You are here!';
const ICON_STANDARD_USER = 'standard:user';
const ERROR_TITLE = 'Error loading Boats Near Me';
const ERROR_VARIANT = 'error';
export default class BoatsNearMe extends LightningElement {
  boatTypeId;
  mapMarkers = [];
  isLoading = true;
  isRendered;
  latitude;
  longitude;
  
  // Add the wired method from the Apex Class
  // Name it getBoatsByLocation, and use latitude, longitude and boatTypeId
  // Handle the result and calls createMapMarkers
  wiredBoatsJSON({error, data}) { }
  
  // Controls the isRendered property
  // Calls getLocationFromBrowser()
  renderedCallback() { }
  
  // Gets the location from the Browser
  // position => {latitude and longitude}
  getLocationFromBrowser() { }
  
  // Creates the map markers
  createMapMarkers(boatData) {
     // const newMarkers = boatData.map(boat => {...});
     // newMarkers.unshift({...});
   }
}
Now that you created the component boatsNearMe, don’t forget to instantiate it in the boatSearchResults.

In the next section, you customize the Boat Details tab, to show the boat details and reviews.

Integrate Third-Party Scripts
You’re a forward-thinker. When the HowWeRoll inventory grows to thousands of boats (hey, no one ever accused you of being a pessimist!), you need to be able to see a list of the best boats at a glance. The script implements a five-star rating scale. During this phase, you complete the development for the fiveStarRating component, which enables users to assign a rating by clicking a gold star.

The component fiveStarRating and its JavaScript file were included in the unlocked package that you installed as part of the prework for this project, so you will be able to find it in your org. You only need to make a few adjustments.

This component must have a read-only mode that outputs the rating but is not clickable.

The image on the left shows the fiveStarRating component in edit mode in the boatAddReviewForm component. The image on the right shows the fiveStarRating in read-only mode in the boatReviews component.

The component fiveStarRating in its two modes. On the left, in edit mode, placed into the addBoatReview component. On the right, the fiveStarRating in read-only mode, placed in the boatReviews component. The edit mode contains a form so the user can enter the Review Subject, the stars for the review rating, and a text editor where the user can enter the Review Comments. There’s a Submit button on the bottom. In read-only mode, the same information is displayed, as read-only fields. Order of the information being displayed: Reviewer name, Review date, Review Subject, Review Comments, Review stars (as starts).

Create the Component
Your newest component, fiveStarRating, has a public value property as well as a public readOnly property. Import the fivestar static resource and call it fivestar. Load the rating.css and rating.js files from the fivestar static resource using the loadScript() function.

After the script loads, invoke a function named initializeRating(). If an error occurs during this process, show the error message using an error toast event, using Error loading five-star for the title. Make sure you implement best practices by storing this string in a constant called ERROR_TITLE and by storing the error variant in a constant called ERROR_VARIANT.

Notice the HTML file has a <ul> tag binding the class attribute to the getter function starClass. This function must use a ternary operator to return either c-rating or readonly c-rating, depending on the value of the readOnly attribute. Make sure you implement best practices and store these strings in constants called EDITABLE_CLASS and READ_ONLY_CLASS respectively.

Notice that the function initializeRating() saves the edited rating value, and calls ratingChanged(rating) to inform the other components about the change. Complete the logic for these functions and use ratingchange for the custom event name that is dispatched.

Now that it’s clear which boat you’re viewing, and that we have a working component with the five-star rating, let’s use it for the boat reviews.

Display Boat Details
It’s time to show details for the selected boat. Create a parent component boatDetailTabs that must be deployed on the top right sidebar of the Lightning page, above the boatMap component.

On the left, the component being displayed before any boat is selected. It shows a message: Please select a boat:). On the right, the component being displayed after a boat has been selected, showing details about the boat, like the boat name, type, length, price, description, and a button on the top right side of the details section, with the label Full Details. This button navigates to the full record on the Boat Record page.

Renata is working with a localization team and, ideally, there would be one custom label for each text displayed to the users. She says creating new labels is something that will be decided internally. For now she requests that you use the existing custom labels. If no boat is selected yet, display a message asking the user to select a boat. Use a custom label for the message named Please_select_a_boat, inside a <span> aligned to the absolute center using SLDS, and no-boat-height, from the boatDetailTabs custom style.

When a boat is selected, the boatTile component fires an event named boatselect that later in this project is handled by boatSearchResults component to update the information about the currently selected boat, and by similarBoats, to open the boat record page.

Inside a scoped <lightning-tabset>, add a <lightning-tab> using the custom label Details as the tab label. Then inside the tab, add a <lightning-card> to receive the details. Use the boatName() value for the title and detailsTabIconName() for the icon name. Here are some important notes.

Ensure that it uses the getRecord method from uiRecordApi to retrieve the values from the fields inside BOAT_FIELDS, using boatId. Assign the retrieved value to wiredRecord.
The function boatName() must leverage the standard function getFieldValue() to extract the boat name from the record wire. Use the right wire adapters and imports to wire the record and get this done.
The detailsTabIconName() function must check if the wiredRecord contains any data. If so, it must return the utility:anchor for the icon. If not, it must return null.
Use the right imports to enable this component to receive information from other components, using the Boat Message channel.
Make sure you are wiring the messageContext in order to subscribe to the message channel.
Based on the design provided by Renata, you need to create a button so the user can navigate to the boat record. Add a <lightning-button> inside the previously created <lightning-card>. Place it inside the actions slot, using the custom label Full_Details as the button label, and invoke the function navigateToRecordViewPage() when the button is clicked to navigate to the record based on the value of boatId.

Adjust the component’s base class in order to navigate to the standard record page. This includes applying the correct extensions to the class.

Display the following pieces of information about the boat:

Type
Length
Price
Description
Add a <lightning-record-view-form> with compact density and the respective <lightning-output-field> for each piece of information. The fields use the currently selected boat Id and Boat__c as the object to retrieve and display the information.

Next, create two more tabs for the Boat Reviews. Inside a <lightning-tab> labeled with the custom label Reviews, instantiate the component boatReviews, passing the currently selected boat Id. Use reviews for this tab’s value property, so you can reference it when navigating back to this tab once the review gets created.

Inside a second <lightning-tab> labeled with the custom label Add_Review, instantiate the boatAddReviewForm component, passing the currently selected boat Id, using the function handleReviewCreated() to handle the custom event named createreview that you will later code inside the boatAddReviewForm component. The function handleReviewCreated() must set the <lightning-tabset> Reviews tab to active using querySelector() and activeTabValue, and refresh the boatReviews component dynamically.

Markup (boatDetailTabs.html)
<template>
  <template if:false={wiredRecord.data}>
    <!-- lightning card for the label when wiredRecord has no data goes here  -->
  </template>
  <template if:true={wiredRecord.data}>
     <!-- lightning card for the content when wiredRecord has data goes here  -->
  </template>
</template>
JavaScript file (boatDetailTabs.js)
// Custom Labels Imports
// import labelDetails for Details
// import labelReviews for Reviews
// import labelAddReview for Add_Review
// import labelFullDetails for Full_Details
// import labelPleaseSelectABoat for Please_select_a_boat
// Boat__c Schema Imports
// import BOAT_ID_FIELD for the Boat Id
// import BOAT_NAME_FIELD for the boat Name
const BOAT_FIELDS = [BOAT_ID_FIELD, BOAT_NAME_FIELD];
export default class BoatDetailTabs extends LightningElement {
  boatId;
  wiredRecord;
  label = {
    labelDetails,
    labelReviews,
    labelAddReview,
    labelFullDetails,
    labelPleaseSelectABoat,
  };
  
  // Decide when to show or hide the icon
  // returns 'utility:anchor' or null
  get detailsTabIconName() { }
  
  // Utilize getFieldValue to extract the boat name from the record wire
  get boatName() { }
  
  // Private
  subscription = null;
  
  // Subscribe to the message channel
  subscribeMC() {
    // local boatId must receive the recordId from the message
  }
  
  // Calls subscribeMC()
  connectedCallback() { }
  
  // Navigates to record page
  navigateToRecordViewPage() { }
  
  // Navigates back to the review list, and refreshes reviews component
  handleReviewCreated() { }
}
CSS (boatDetailTabs.css)
.no-boat-height {
  height: 3rem;
}
At this point, you can browse and filter boats, and view boat details. Next you add and display reviews, write secure JavaScript, integrate third-party scripts, and plot your boats on a map.

Add Reviews to the Boats
Since HowWeRoll doesn’t own all of these boats, it’s important to keep track of positive and negative experiences when leasing them out to clients. This way, you can remove boats with issues or defects. Accomplish this objective by adding the ability to submit reviews for each boat.

Clicking the Submit button performs the following actions:

Creates a new record in BoatReview__c (using Lightning Data Service)
Displays a toast message that the submission was successful
Activates the Reviews tab, showing the list of reviews (boatReviews component) for the selected boat
The component boatReviewForm being displayed. The tab label is Add Review. It contains a form so the user can create a review, entering the Review Subject, the stars for the review rating, and a text editor where the user can enter the Review Comments. There’s a Submit button on the bottom.

Build the Form
The Add Review tab of the boatDetailTabs component instantiates a new component boatAddReviewForm, passing the selected boat’s Id as to the public setter named recordId. This setter must store the value of recordId into boatId.

Tatiana started working on the component’s layout to display the input fields, but she wasn’t able to finish it. For the Lightning Data Service code, you need a <lightning-record-edit-form> referencing the BoatReview__c object. On the <lightning-record-edit-form> level, use the function handleSubmit() to submit the form, and handleSuccess() to show the success message using a success toast event titled Review Created!. Following best practices, store the title string in a constant called SUCCESS_TITLE and store the variant string on a constant called SUCCESS_VARIANT.

The Review Subject and Comment fields are bound to the Name and Comment__c fields from the BoatReview__c object, using Lightning data service. Therefore you need to use a label with the class slds-form-element__label for the <lightning-input-field> bound to record Name. Hide the real field’s label using the variant label-hidden.

Add the fiveStarRating component completed in the previous step to the form so that users can enter their rating. The fiveStarRating component triggers a custom event called ratingchange, and then boatAddReviewForm handles it with the function handleRatingChanged.

Use the following table for the field’s requirements.

Field	API Name	Label	Schema import	Required?
Boat	Boat__c	N/A: Field is not displayed to the user	N/A: Field is not displayed to the user	Yes. Based on the currently selected Boat Id.
Name	Name	'Review Subject' in labelSubject	NAME_FIELD	Yes.
Rating	Rating__c	'Rating' in labelRating	N/A: Field is handled by the fiveStarRating component	Yes. Default = 0.
Comment	Comment__c	'Comment', inherited from the field	COMMENT_FIELD	Yes.
The Submit button has Submit for the label, and it uses the utility:save icon. Display error messages above or below the form fields automatically.

Create a New BoatReview Record
In other words, the component leverages Lightning Data Service to create a BoatReview__c record. Once the record is submitted for insert, the function handleSubmit() populates the Boat__c and the Rating__c fields before inserting the record into the database.

The lightning-record-edit-form must use the value of boatReviewObject, that must retrieve BoatReview__c from the BOAT_REVIEW_OBJECT schema import. NAME_FIELD must import the BoatReview__c.Name field, assigned to nameField, and COMMENT_FIELD must import the BoatReview__c.Comment__c field, assigned to commentField.

After the form successfully inserts the record, it calls a handleSuccess() function, which shows the success toast message and triggers a custom event called createreview. It also calls the handleReset() function, which clears the form’s data.

This component also has a function called handleRatingChanged() that updates the private property rating every time the five-star rating is updated. The value of this property must be saved into Rating__c prior to the form submission.

Markup (boatAddReviewForm.html)
<template>
  <!-- lightning data service code -->
  <!-- <lightning-record-edit-form> using boatReviewObject -->
    <lightning-layout multiple-rows vertical-align="start">
      <lightning-layout-item size="8" padding="horizontal-small">
        <div class="slds-form-element">
           <!-- review subject field code using nameField -->
        </div>
      </lightning-layout-item>
      <lightning-layout-item size="4" padding="horizontal-small">
        <div class="slds-form-element">
          <label class="slds-form-element__label" for="record-name">Rating</label>
          <div class="slds-form-element__control">
            <!-- add five star rating component -->
          </div>
        </div>
      </lightning-layout-item>
      <lightning-layout-item padding="horizontal-small">
           <!-- review comment field code using commentField -->
      </lightning-layout-item>
    </lightning-layout>
    <div class="slds-align_absolute-center" style="margin-top: 5px">
           <!-- add submit button -->
    </div>
  <!-- </lightning-record-edit-form> -->
</template>
JavaScript file (boatAddReviewForm.js)
// imports
// import BOAT_REVIEW_OBJECT from schema - BoatReview__c
// import NAME_FIELD from schema - BoatReview__c.Name
// import COMMENT_FIELD from schema - BoatReview__c.Comment__c
export default class BoatAddReviewForm extends LightningElement {
  // Private
  boatId;
  rating;
  boatReviewObject = BOAT_REVIEW_OBJECT;
  nameField        = NAME_FIELD;
  commentField     = COMMENT_FIELD;
  labelSubject = 'Review Subject';
  labelRating  = 'Rating';
  
  // Public Getter and Setter to allow for logic to run on recordId change
  get recordId() { }
  set recordId(value) {
    //sets boatId attribute
    //sets boatId assignment
  }
  
  // Gets user rating input from stars component
  handleRatingChanged(event) { }
  
  // Custom submission handler to properly set Rating
  // This function must prevent the anchor element from navigating to a URL.
  // form to be submitted: lightning-record-edit-form
  handleSubmit(event) { }
  
  // Shows a toast message once form is submitted successfully
  // Dispatches event when a review is created
  handleSuccess() {
    // TODO: dispatch the custom event and show the success message
    this.handleReset();
  }
  
  // Clears form data upon submission
  // TODO: it must reset each lightning-input-field
  handleReset() { }
}
Change the Tab Focus to the Reviews Tab
Let’s piece it together. Earlier in this project you created a function named handleReviewCreated() inside the component boatDetailTabs, right? Good!

After the review is successfully saved, the custom event createreview is fired back to the boatDetailTabs parent component, which then listens for the event, calls the function named handleReviewCreated() and sets the Reviews tab as the currently selected tab.

Display Boat Reviews for the Selected Boat
Now that we’ve given users the ability to add reviews, let’s display them. Each boat can have many reviews.

The Reviews tab of the boatDetailTabs component instantiates a new component, boatReviews, passing the selected boat’s Id as to the public setter named recordId. This setter must store the value of recordId into boatId and query the review records invoking getReviews().

The BoatDataService Apex class defines a method named getAllReviews() that accepts an argument named boatId of type Id and returns a list of BoatReview__c. Review the method to check which fields are being returned. Make sure the getAllReviews() method is able to pull the newly created reviews, not a cached list.

The component imports this method with the name getAllReviews. This method is called imperatively from the getReviews() function, and stores the return value on the private property boatReviews.

Develop a getter named reviewsToShow() that returns true if boatReviews is not null, not undefined, and if it has at least one record. Otherwise, it returns false.

The boatReviews component defines an independently scrolling area using a Lightning component. If no reviews are found, it outputs the text No reviews available bound to the reviewsToShow() getter function. The text is absolutely positioned at the center within the scrollable region.

The component boatReviews being displayed. The tab label is Reviews. There is only a message in the absolute center of the component: No reviews available.

Then, add a second <div> below it that displays only if reviewsToShow() returns true. This <div> must use the following style classes to maintain the design suggested by Renata: slds-feed, reviews-style, slds-is-relative, slds-scrollable_y. (Make sure to remove the commas when copying these style classes!) This section holds all the content related to the reviews to be displayed.

Include a small <lightning-spinner> in the boatReviews as well, using brand for the variant, use Loading as the alternative text, and the attribute isLoading via the method getReviews() to check if it’s still loading. If boat reviews are found, it uses an iteration property named boatReview to output all of the fields specified in the getAllReviews Apex method.

Below it, show the list of reviews. The output must conform to the Feed component blueprint from SLDS.

For each boat review, display the avatar of the user who created the review inside a circled <lightning-avatar> using the SmallPhotoUrl field. In front of the avatar, show the user’s name, followed by the user’s company name. Right below it, show the date when the review was created using a <lightning-formatted-date-time> tag.

Sometimes, you want to know more about the person leaving the review, so the CreatedBy field is hyperlinked, invoking a JavaScript function named navigateToRecord() which uses the Lightning navigation service to navigate to the record, landing on the standard user record page. The link contains a data-record-id attribute that holds the value of boatReview.CreatedBy.Id. The link’s title must be the author’s name. The navigateToRecord() function retrieves the value from the data-record-id attribute that was encoded on the hyperlink and fires an event that takes the user to the detail page of the review’s author.

Create a <div> with the class slds-text-longform to receive the review subject inside a <p> with the class slds-text-title_caps, the review comment inside a <lightning-formatted-rich-text>, and the review rating, again using the fiveStarRating in read-only mode.

The component boatReviews being displayed. The tab label is Reviews.The same information is displayed, as read-only fields. Order of the information being displayed for each review: Reviewer avatar, Reviewer name, Review date, Review Subject, Review Comments, Review stars (as stars).
Make sure each boat review is inside an article, using slds-post for the class, and each article has a header, using slds-post__header slds-media for the class. This gives Renata the look and feel she’s looking for.

This component also has a public function called refresh() which refreshes the list of reviews, calling getReviews().

Those were a lot of details! I bet you’re excited to see the existing snippets you’re going to work with. Let’s take a look.

Markup (boatReviews.html)
<template>
  <!-- div for when there are no reviews available -->
  <div>No reviews available</div>
  <!-- div for when there are reviews available -->
  <div>
  <!-- insert spinner -->
    <ul class="slds-feed__list">
      <!-- start iteration -->
        <li class="slds-feed__item" key={boatReview.Id}>
          <article class="slds-post">
            <header class="slds-post__header slds-media">
              <div class="slds-media__figure">
                <!-- display the creator’s picture -->
              </div>
              <div class="slds-media__body">
                <div class="slds-grid slds-grid_align-spread slds-has-flexi-truncate">
                  <p>
                      <!-- display creator’s name -->
                    <span><!-- display creator’s company name --></span>
                  </p>
                </div>
                <p class="slds-text-body_small">
                  <!-- display created  date -->
                </p>
              </div>
            </header>
            <div class="slds-text-longform">
              <p class="slds-text-title_caps"><!-- display Name --></p>
              <!-- display Comment__c -->
            </div>
            <!-- display five star rating on readonly mode -->
          </article>
        </li>
      <!-- end iteration -->
    </ul>
  </div>
</template>
JavaScript file (boatReviews.js)
// imports
export default class BoatReviews extends LightningElement {
  // Private
  boatId;
  error;
  boatReviews;
  isLoading;
  
  // Getter and Setter to allow for logic to run on recordId change
  get recordId() { }
  set recordId(value) {
    //sets boatId attribute
    //sets boatId assignment
    //get reviews associated with boatId
  }
  
  // Getter to determine if there are reviews to display
  get reviewsToShow() { }
  
  // Public method to force a refresh of the reviews invoking getReviews
  refresh() { }
  
  // Imperative Apex call to get reviews for given boat
  // returns immediately if boatId is empty or null
  // sets isLoading to true during the process and false when it’s completed
  // Gets all the boatReviews from the result, checking for errors.
  getReviews() { }
  
  // Helper method to use NavigationMixin to navigate to a given record on click
  navigateToRecord(event) {  }
}
CSS (boatReviews.css)
.reviews-style {
  max-height: 250px;
}
Deploy the Component

Instantiate the component in the boatAddReviewForm component and the boatReviews component in the boatDetailTabs.

It’s time for the final step on this journey! You’re almost there—but your voyage isn’t quite over yet.

Convert the Similar Boats Page to Lightning
Weimar Williams, the Salesforce admin at HowWeRoll, informed you that there is a Visualforce page that needs to be converted to Lightning. It’s a page named SimilarBoats, and it uses a Visualforce component named SimilarBoatsComponent to show a list of boats that are similar to the one being displayed. A button that navigates to this page can be found on the Boat__c record page.

On the left, the Boat__c record page showing the Similar Boats button, that is placed on the upper right side of the screen. On the right, the Visualforce page SimilarBoats details about a Boat, and right below it, a list of similar boats, using the SimilarBoatsComponent.

This Visualforce page must be used as an example to create a reusable Lightning web component called similarBoats.

Weimar wants you to empower admins to configure this component, so make the changes in order for this component to be used on the Boat Record Page. It shows boats that are similar, based on the property they want the boat to be compared by: Type, Price, or Length. Make the adjustments to the metadata and label the filter as Enter the property you want to compare by.

Notice the Lightning page named Boat_Record_Page included in the unlocked package. Perform the required adjustments in order to make sure the Current Boat Location component is on the right sidebar, showing the position of the current boat. Each instance of similarBoats is placed right below the Current Boat Location component.

Boat Record Page showing the current boat details on the left, then the component Current Boat Location on the right side bar (top) and the Similar Boats on the right side bar (bottom), filtering the boats by Type.

This is the right sidebar with the component similarBoats and the filters created in the metadata:

Boat Record Page in edit mode, showing the three different properties the similar boats can be filtered by: Type, Price, Length.

Each instance of this reusable component displays similar boats based on the parameter used. These similar boats are retrieved by an Apex method called getSimilarBoats, from the class BoatDataService. This method accepts the boat Id (boatId) and a String (similarBy) as parameters to query the similar boats. According to Byanca, the logic in the Apex method is already working as intended, so you don’t need to change it.

However, you do need to make the adjustments to the component’s JavaScript file to wire this apex call and store the result of this call into the relatedBoats property. This is the property that is used by the getter noBoats() and the template for loop in the HTML portion.

Note that this component is also reusing the boatTile and its existing behavior, so all you need to do is to instantiate this component for each boat in the relatedBoats list. Pass the boat (for loop item) as a parameter, and use the openBoatDetailPage() function for the onboatselect event that navigates to the similar boat record using standard Lightning navigation based on the similar boat’s Id.

Renata requested that the <lightning-layout-item> used to contain each boatTile also be responsive. So apply the following properties to it.

Property	Value
Padding	"around-small"
Standard size	"12"
Size on small devices	"6"
Size on medium devices	"4"
Size on large devices	"4"
The responsive design must be able to change the tile and images sizes. Here’s one example of this component, showing similar boats by Type, but on an expanded browser window.

An instance of the component similarBoats, showing similar boats by Type, and the tiles are much larger, in width and length, being automatically resized by the applied style.

Don’t forget to place the new similarBoats component on the record page three times, once for each property filter.

Markup (similarBoats.html)
<template>
  <lightning-card title={getTitle} icon-name="custom:custom54">
    <lightning-layout multiple-rows="true">
      <template if:true={noBoats}>
        <p class="slds-align_absolute-center">There are no related boats by {similarBy}!</p>
      </template>
      <!-- Loop through the list of similar boats -->
      <template ...>
        <!-- Responsive lightning-layout-item -->
        <lightning-layout-item >
          <!-- Each boat tile goes here -->
        </lightning-layout-item>
      </template>
    </lightning-layout>
  </lightning-card>
</template>
JavaScript file (similarBoats.js)
// imports
// import getSimilarBoats
export default class SimilarBoats extends LightningElement {
  // Private
  currentBoat;
  relatedBoats;
  boatId;
  error;
  
  // public
  get recordId() {
      // returns the boatId
    }
    set recordId(value) {
        // sets the boatId value
        // sets the boatId attribute
    }
  
  // public
  similarBy;
  
  // Wire custom Apex call, using the import named getSimilarBoats
  // Populates the relatedBoats list
  similarBoats({ error, data }) { }
  get getTitle() {
    return 'Similar boats by ' + this.similarBy;
  }
  get noBoats() {
    return !(this.relatedBoats && this.relatedBoats.length > 0);
  }
  
  // Navigate to record page
  openBoatDetailPage(event) { }
}
