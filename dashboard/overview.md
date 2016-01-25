#Dashboard Overview

Much of the functionality in Tidal apps is contained in dashboards.  They are where admins perform their work to make the
system function, and sometimes where members of the public write content.

##Creating a dashboard page
A simple dashboard page has two elements
 - a plugin
 - a view

The plugin file contains buiness logic to load, transform, and act on data.  Perhaps most commonly loading a set of items
(such as users) in a chunk for paginated display.

The view file should not contain much logic but controls how the data passed to it is presented.

## Simple plugin file

Create a file in a Gator checkout at `tidal/application/dashboards/manage/plugins/Helloworld.php`

```
<?php

namespace Gator\Dashboard\Manage;
use View;
use Request;

defined('SYSPATH') or die('No direct script access.');

class Helloworld extends \Gator\Dashboard
{

    public function before()
    {
        $this->check_role('login');
        View::set_global('_heading', 'Hello World');

        $this->request = Request::initial();
        $this->params = $this->request->post() + $this->request->query();

        // maybe load some other data that should always be available in this dashboard section
    }

    public function page_hello()
    {

        // generally we are fetching data here to populate the page

        $page_data = "Hello World!";

        return View::factory('helloworld/hello')
            ->set('page_data', $page_data);
    }
}
```

Note that the classname (and filename) control routing, so a call to an url like `http://lucky.gator.com/manage/do/page/helloworld/hello`
will be routed to `\Gator\Dashboard\Manage\Helloworld::page_hello()`.

We normally use `before()` to do any global setup this dashboard section needs. That might mean loading data, but it
always means checking that the logged in user has ACL permission to access this area.  In the sample above it's just
checking they have the role 'login' but rights can be fine-grained.

The method that controls this page, `page_hello()` is not doing much in this sample.  It's setting a variable to be passed to the view, then
creating and returning a view instance.

## Simple View File
```
<div class="container">
	<div class="row">
		<div class="<?=($_REQUEST['show-sidebar'] ? 'span8' : 'span12')?> pane">
			<?= View::factory('/builder/widget')
					->set([
                        'title' => [
                            'icon' => 'fa-user',
                            'badge' => $count,
                            'cog' => 1,
                            'text' => 'Sample Page'
                        ],
						'content' => [
							$page_data
						],
					]);

			?>

		</div> <!-- .span12/8 -->

		<div class="span4 sidebar" style="<?=($_REQUEST['show-sidebar'] ? '' : 'display:none')?>">
				<?=View::factory('helloworld/widgets/sample')?>
		</div>

	</div>
</div>
```
To match the name given in the plugin, your view file should be at `tidal/application/dashboards/manage/views/helloworld/hello.php`.

Views will generally be a mixture of HTML, simple PHP and calls to reusable helpers and fragments that will generate common HTML.
`builder/widget` above is often use to wrap boxed content in consistently styled divs and headers.  It also (optionally) presents the
cog icon to access the right sidebar.

This structure is commonly used, with a wide left column and a hideable narrower right column.  The right column here has a widget in it.  That's the final
file needed to make this sampple work.

## Simple Widget File
 ```
 <?= View::factory('/builder/widget')
 		->set([
 			'id' => 'sample-widget',
             'title' => [
                 'icon' => 'fa-flask',
                 'text' => 'Drink Me'
             ],
 						'content' => [
 							'<p>This button does nothing</p>'.
 				'<a class="btn margin-bottom replace-widget" href="#">Drink Me</a>'
 			]
 	]);
 ?>
 ```
The call to `View::factory('helloworld/widgets/sample')` means we need the file at
 `tidal/application/dashboards/manage/views/helloworld/widgets/sample.php`. Again we are using Builder\Widget as a wrapper to control
 style.
