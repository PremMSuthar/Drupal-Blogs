# Advanced Drupal Debugging in 2025: Step-by-Step Setup, Code, and Visual Guide

Debugging Drupal sites is easier and more powerful than ever, thanks to robust toolsets and clear best practices. Here’s a deep dive on how to set up your Drupal debugging environment, code examples for common tasks, and guidance on what images/screenshots to include for maximum clarity in your blog.

## 1. **Setting Up Your Debugging Environment**

### **A. Enable Error Reporting**

- Edit your `settings.php` (located in `sites/default/`):

```php
// Enable all error reporting for development.
error_reporting(E_ALL);
ini_set('display_errors', TRUE);
ini_set('display_startup_errors', TRUE);
// Recommended: Only for development, never production!
```

- **Image Suggestion:** Screenshot of the Drupal site showing error messages displayed at the top of the page.


### **B. Install Devel Module**

1. **Install using Composer:**

```bash
composer require drupal/devel
```

2. **Enable via Drush:**

```bash
drush en devel --yes
```

3. Access Devel’s configuration and settings at `/admin/config/development/devel`.

- **Image Suggestion:** admin panel showing the Devel settings page.


#### **Practical Devel Code Examples**

- Print debugging info for a variable:

```php
// Example: Inspect a node object in a custom module.
dpm($node, 'Current node object');
```

This displays a readable dump directly in the Drupal UI, labeled for easy searching.
- Inspect database queries:

```php
// After running complex entity queries, inspect with:
dpq($query);
```


### **C. Enabling Xdebug (With PhpStorm/VSCode)**

1. Install Xdebug (in your Docker/DDEV or local PHP setup):

```bash
pecl install xdebug
```

2. Update your `php.ini` with:

```
zend_extension=xdebug.so
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_port=9003
xdebug.client_host=host.docker.internal
```

3. Set breakpoints in your IDE, then reload your Drupal page to trigger debugging.

- **Image Suggestion:** IDE screenshot showing a breakpoint and a stack trace during a request.


## 2. **Twig Theming Debugging \& Template Discovery**

### **Enable Twig Debug Mode**

Edit `sites/default/services.yml`:

```yaml
parameters:
  twig.config:
    debug: true
    auto_reload: true
    cache: false
```

Clear cache:

```bash
drush cr
```

Now, in your site’s HTML source, you’ll see comments like:

```html
<!-- THEME DEBUG -->
<!-- FILE NAME SUGGESTIONS:
   * node--article.html.twig
   * node.html.twig
-->
<!-- BEGIN OUTPUT from 'themes/custom/mytheme/templates/node--article.html.twig' -->
```

- **Image Suggestion:** Browser DevTools open to the HTML showing theme debug comments.


### **Twig Variable Dump Example**

Inside any Twig template:

```twig
{{ dump() }}
{{ dump(content) }}
```

This outputs the template variables right within your page (visible if Twig debug is enabled).

## 3. **Command-Line Debugging Workflows**

### **Using Drush for Diagnostics**

- Get overall status:

```bash
drush status
```

- Check enabled modules:

```bash
drush pm:list --status=enabled
```

- Clear caches quickly:

```bash
drush cr
```

- **Image Suggestion:** Terminal output of `drush status` and `drush cr` commands.


### **Automated Testing with PHPUnit**

- Write a simple test:

```php
// In modules/custom/my_module/tests/src/Functional/MyTest.php
public function testHomepageLoads() {
  $this->drupalGet('/');
  $this->assertSession()->statusCodeEquals(200);
}
```

- Run tests:

```bash
phpunit --testsuite functional
```


---

## 4. **Cache Configuration for Debugging**

- To disable render and page caching during development (add to `settings.local.php`):

```php
$settings['cache']['bins']['render'] = 'cache.backend.null';
$settings['cache']['bins']['dynamic_page_cache'] = 'cache.backend.null';
```

- Remember: **Never commit these lines for production.**


## 5. **Visual Guide Suggestions for the Blog**

- **Drupal error visible screenshot** (proves error reporting is enabled)
- **Devel module admin UI** (visual guide to its features)
- **IDE with Xdebug Breakpoint** (demonstrates backend step-debugging)
- **HTML View with Twig Comments** (shows template suggestions)
- **CLI output for Drush/Console** (easy for readers to replicate)
- **Sample Log Messages** in Reports > Recent log messages (Drupal admin screenshot)


## 6. **Useful Debugging Recipes**

### **Backtrace Debugging Example**

Insert in your custom module to trace execution:

```php
debug_print_backtrace(DEBUG_BACKTRACE_IGNORE_ARGS);
die('Stopping here for backtrace.');
```

- Use sparingly and never in production.


### **Log a Custom Message**

```php
\Drupal::logger('my_module')->notice('Custom debug value: @value', ['@value' => $my_value]);
```

These messages appear in **Reports > Recent log messages**.

## Conclusion

With this expanded toolkit, you can set up a powerful debugging environment for Drupal, understand every layer of your site, and quickly resolve issues in code, configuration, or theming. Combining **clear error reporting, Devel, Xdebug+IDE, Drush/Console, Twig debug, and detailed logging**, you’ll troubleshoot with confidence and efficiency.

**Tip for the blog:** Consider including step-by-step screenshots for each major step, as visual context can dramatically accelerate learning for new and intermediate Drupal developers. Mark up key parts of screenshots (settings, buttons, code highlighted) with arrows or circles for clarity.

If you need even more specifics, want example screenshots, or want to focus on a particular area (like frontend vs backend debugging), let me know!

