comploader
==========

**comploader** is a *web component* loader for sane/practical people.

A *web component* is anything that consists of one or more JavaScript/CSS files.

comploader lets you:
 * register/define components (name and configuration)
 * load components on demand

A component can:
 * include many CSS files
 * include many JavaScript files
 * depend (and be loaded after) other components


What problem does this solve?
-----------------------------

On-demand loading of web resources (JavaScript/CSS libraries).


Usage
-----

	<script type="text/javascript" src="comploader.js"></script>

	<!-- Define the components (only got one here) -->
	<script type="text/javascript">
		comploader.register("jquery", {
			"scripts": ["/path/to/jquery.js"]
		});
	</script>

	<!-- Make use of the components -->
	<script type="text/javascript">
		comploader.load("jquery", function () {
			//jQuery is now loaded - use it.
			$(document.body).css('backgroundColor', 'red');
		});
	</script>


See the examples directory for more.


What if a component depends on other components?
------------------------------------------------

No problem.
Tell comploader about the dependency and it will load components in the correct order.

	<!-- Define the components -->
	<script type="text/javascript">
		comploader.register("jquery", {
			"scripts": ["/path/to/jquery.js"]
		});

		comploader.register("jqueryui", {
			"requires": ["jquery"],
			"stylesheets": ["/path/to/jquery-ui.css"],
			"scripts": ["/path/to/jquery-ui.js"]
		});
	</script>

	<!-- Make use of the components -->
	<script type="text/javascript">
		comploader.load("jqueryui", function () {
			//Both components are now loaded (jQuery 1st and then jQuery UI)
		});
	</script>


How is this different than RequireJS?
-------------------------------------

[RequireJS](http://www.requirejs.org/) is focused on JavaScript files (modules) written in a certain special way ([AMD](https://github.com/amdjs/amdjs-api/wiki/AMD)).

Most web libraries **ARE NOT** written like that and thus **DO NOT** support RequireJS.
Using such libraries in RequireJS projects is a pain.

jQuery plugins also do not fit well into the "modules" paradigm.

Additionally, some libraries (Twitter Bootstrap, jQuery UI) often include CSS stylesheets (not just JavaScript files).
RequireJS does not handle CSS loading.

To summarize:
 * RequireJS is a pain to use (in general) - just [read about it](http://www.requirejs.org/)
 * RequireJS is a pain to use (for loading libraries lacking AMD support)
 * RequireJS does not handle loading of CSS files
 * RequireJS is huge and complex compared to comploader (81KB vs 4KB)


How do I later minify/combine some of my components?
----------------------------------------------------

comploader cannot optimize and combine your resources like RequireJS claims to do.
It's your job to decide what should be combined and how that's done.

Suppose that at first you had a "jqueryui" component registered and loaded on demand with comploader.

You later decide that:
 * it should be loaded via regular standalone `<link>` and `<script>` tags
 * it should be combined/minified into your combined.css/combined.js files (how you do that is up to you)

To keep all your existing code (and components that depend on "jqueryui") working, you only need to make minor changes.

Before:

	<script type="text/javascript">
		comploader.register("jqueryui", {
			"stylesheets": ["/path/to/jquery-ui.css"],
			"scripts": ["/path/to/jquery-ui.js"]
		});
	</script>

After:

	<!-- jQuery UI is now is now part of combined.css/combined.js -->
	<link rel="stylesheet" href="combined.css" />
	<script type="text/javascript" src="combined.js"></script>

	<!--
		Register (or re-register) the jqueryui component with an empty configuration.
		Code that later calls `comploader.load("jqueryui")` will consider it loaded.
	-->
	<script type="text/javascript">
		comploader.register("jqueryui", {});
	</script>
