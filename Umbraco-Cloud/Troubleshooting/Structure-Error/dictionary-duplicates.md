# Troubleshooting duplicate dictionary items

If your site has been using Courier in the past and you have dictionary items that have been deployed between your environments, you may see errors and/or duplicated dictionary items when your site is upgraded to using Umbraco Deploy.

## Cause

Due to how Umbraco Courier has been handling dictionary items in the past, old sites having used Courier to transfer these - are currently very likely to see errors when trying to use Deploy with these items.

Courier handled dictionary items only using their `ItemKey` (alias) and did not take into account that dictionary items also carried a unique identifier. Since this unique identifier was not known or handled by Courier - it would not be carried over to other environments when these dictionary items were deployed. As Courier never cared about or used this unique identifier - everything seemed to be in order most of the time.

Upgrading to Deploy, one of the major improvements is that Deploy handles everything by its unique identifier making it much more stable. However when dictionary items are in a state of not having consistent unique identifiers across each individual environment - they will simply not work with Deploy unless they are syncronized.

## Fixing

In order to fix this issue, it is required that all dictionary items are aligned to have the same unique identifier for a specific `ItemKey` across all environments. The easiest way of doing this is to select an environment where you believe all your dictionary items are mostly correct, remove any duplicated items and then ensure that the changes are pushed to your other environments:

1. Ensure you do not have any duplicated dictionary items in your UDA files. If you do - these will need to be cleaned up as a first step.

2. Ensure you remove all duplicate entries in the backoffice if any.

3. When you can successfully create a `deploy` marker and get a `deploy-complete` - your environment should be "clean" and you are good to go.

4. Create a `deploy-repairdictionaryids` marker in the `/data/` folder.

Doing this will make Deploy run through all of the dictionary items in the database and remove any duplicates (there should be none, if you did the manual cleanup correctly).

When done with this, it will go through all UDA files and check if there is any duplicates existing in your site for that particular `ItemKey` used in that UDA file. If it finds a duplicate - it will update the ID to match, so your dictionary item is syncronized with the UDA file.

5. When this is done - you will need to transfer your UDA files to the next environment, to ensure the same ID's will be applied here.

6. Create a `deploy-repairdictionaryids` marker in the `/data/` folder.

Deploy will now again delete any existing duplicate entries and update the ID's of the remaining entries to match the IDs in the UDA files.

7. Repeat steps 5-6 if there's any other environments.

## Important Notes

What Deploy will **not** help you with, is duplicated items that do not have a corresponding UDA file. Since we do not know what to do with a dictionary item that we don't have a matching UDA file for - if we find duplicates with no UDA files, we will simply remove one of them to ensure there are no duplicate errors. For this item to be deployed, you will need to recreate a UDA file for it - this can be done simply by saving the item through the backoffice.
