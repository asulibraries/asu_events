# Standalone notes

Where all the bits come from.

## Templates
Template file source:
web/themes/webspark/renovation/templates/block/block--inline-block--events.html.twig

Change line 13 to {{ attach_library('asu_events/reactjs') }}

## asu_events.info.yml

Change dependencies to
- fontawesome
- fontawesome_iconpicker_widget

Fontawesome and the iconpicker dependencies are transferred from webform_blocks module so we don't have to use that.

Add reactjs as a local library:
reactjs:
  version: 1.x
  js:
    node_modules/react/umd/react.production.min.js: {minified: true, weight: -10}
    node_modules/react-dom/umd/react-dom.production.min.js: {minified: true, weight: -10}

Copy react and react-dom js files from asu_react_integration/node_modules to asu_events/node_modules

## Gitignore

Modify .gitignore file to include PHPStorm .idea folders, etc. if it doesn't already have it and exclude /node_modules/react and /react-dom folders.

## Misc
Rename asu_events.install to asu_events.install.ignore so it doesn't run.


