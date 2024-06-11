# Standalone notes

Where all the bits come from.

## asu_events.info.yml

Change dependencies to
- fontawesome
- fontawesome_iconpicker_widget

Fontawesome and the iconpicker dependencies are transferred from webform_blocks module.

## asu_events.libraries.yml

Add reactjs as a local library:
```
reactjs:
  version: 1.x
  js:
    node_modules/react/umd/react.production.min.js: {minified: true, weight: -10}
    node_modules/react-dom/umd/react-dom.production.min.js: {minified: true, weight: -10}
```

Copy react and react-dom js files and directories from asu_react_integration/node_modules to asu_events/node_modules

## Templates
Template file source:
web/themes/webspark/renovation/templates/block/block--inline-block--events.html.twig

Filename changed to entity-embed-container--embed-button--events.html.twig

Add `'block-' ~ 'asu-events',` to `classes`

Change `{{ attach_library('asu_react_integration/reactjs') }}` to `{{ attach_library('asu_events/reactjs') }}` on line 13 (now 16).

## asu_events.module

Change the hook_preprocess_block() function to hook_preprocess_entity_embed_container(). Pull various info and set other info to match what the events scripts are looking for.

Add code to the module file to add a theme suggestion for embed containers + block_type and also point our module to the new template file.

Here is the complete modified asu_events.module file.
```markdown
/**
* This is modified heavily from the version in Webspark 2.0.
*
* We embed event blocks into our content via entity embed which
* renders the block_content but not as a block. So we have to change
* the preprocess functions to work with entity embed containers instead
* of blocks.
  */


/**
* Implements hook_preprocess_HOOK().
*
* {@inheritdoc}
*
* Combined the old library events module with the new webspark
* module in this function.
* Beginning bits are from the previous version of
* the events module and the rest is from the new version.
*
*/
function asu_events_preprocess_entity_embed_container(&$variables) {
$element = $variables['element'];
$block = $element['#entity'];
$block_type = $block->bundle();
if ($block_type == "events") {
$variables['base_plugin_id'] = 'inline_block';
$variables['derivative_plugin_id'] = 'events';
$variables['content']['#block_content'] = $block;

    /* Moved the following from block_alter function to this one */
    $rand_id = random_int(0, PHP_INT_MAX);
    $events_block = (object)[
        'header' => new \stdClass(),
        'ctaButton' => new \stdClass(),
        'dataSource' => new \stdClass(),
    ];

    $variables['events_id'] = 'events-wrapper-' . $rand_id;
    $block = $variables['content']['#block_content'];

    if ($block->field_events_heading->value) {
      $events_block->header->text = $block->field_events_heading->value;
    }
    if ($block->field_events_header_text_color->value) {
      $events_block->header->color = $block->field_events_header_text_color->value;
    }
    if ($block->field_events_cta && $block->field_events_cta->title && $block->field_events_cta->uri) {
      $events_block->ctaButton->text = $block->field_events_cta->title;
      $link = Url::fromUri($block->field_events_cta->uri);
      $events_block->ctaButton->url = $link->toString();
    }
    if ($block->field_events_cta_color->value) {
      $events_block->ctaButton->color = $block->field_events_cta_color->value;
    }
    if ($block->field_events_items_to_display->value) {
      $events_block->display = $block->field_events_items_to_display->value;
    }
    $events_block->dataSource->url = '/asu-events-feed/';
    if ($block->field_feed_taxonomy_term->value) {
      // Allow only alphanumeric characters and underscore.
      $events_block->dataSource->url .= preg_replace('/[^A-Za-z0-9\_]/', '', $block->field_feed_taxonomy_term->value);
    }
    if ($block->field_events_feed_filter->value) {
      $events_block->dataSource->filters = $block->field_events_feed_filter->value;
    }

    $settings = [];
    $settings['components']['asu_events'][$rand_id] = $events_block;

    $variables['content']['#attached']['drupalSettings']['asu'] = $settings;
    $variables['content']['#attached']['library'][] = 'asu_events/events-component';

}
}

/**
 * Implements hook_theme().
 *
 * {@inheritdoc}
 *
 * Tell the module to use the correct theme file - we need
 * entity embeds rather than inline blocks.
 */
function asu_events_theme($existing, $type, $theme, $path) {
  $templates = $path . '/templates';
  return [
      'entity_embed_container__embed_button__events' => [
          'base hook' => 'entity_embed_container',
          'render element' => 'element'
      ]
  ];
}

/**
 * Implements hook_theme_suggestions_alter()
 *
 * @param array $suggestions
 * @param array $variables
 * @param $hook
 * @return void
 *
 * Add a theme suggestion for entity embed button plus the block type.
 *
 */
function asu_events_theme_suggestions_alter(array &$suggestions, array $variables, $hook) {
  if (!empty($variables['element']['#entity'])) {
    $block_type = $variables['element']['#entity']->bundle();
    $suggestions[] = 'entity_embed_container__embed_button' . '__' . $block_type;
  }
}
```

## Gitignore

Modify .gitignore file to include PHPStorm .idea folders, etc. if it doesn't already have it and exclude /node_modules/react and /react-dom folders.

e.g. Add at line 4:
```
!node_modules/react
!node_modules/react-dom
```

## Config

Add an `optional` directory to config.
Move everything except `asu_events.settings.yml` to the optional directory.

## Misc
Rename asu_events.install to asu_events.install.ignore so it doesn't run.


