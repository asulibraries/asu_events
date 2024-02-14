# ASU Events

The ASU Events module provides the events component for Webspark.
The component retrieves content from a feed and uses 2 types of filters:
- Feed taxonomy term (See: https://asuevents.asu.edu/reports/taxonomy-terms)
- Feed Filter (A comma separated list of filters)

The component can be displayed like:
- Three cards arrangement
- Three items in a vertical list
- All items in a vertical list

## Installation instructions
The ASU Events Integration module installs when you create a Webspark site.
If you're using this module outside of Webspark or in other situations, see the instructions below.
1. Add VCS for this github repo in composer.json
```
{
            "type": "vcs",
            "url": "https://github.com/asulibraries/asu_events"
        }
```
2. Composer require the module - `composer require asulibraries/asu_events:dev-main`
3. Enable the ASU Events module just like any other module.
4. Go to Admin -> Structure -> Blocks and create a custom block -> Events.
5. Embed the block via layout builder or entity embed on a page.


## Requirements

Drupal 8.x, 9.x, or 10.x.
