# menufy
### The magic menu manager. 
### A simple professional way to manage menu list

Menufy is a class that manages a system or panel menu. \
When creating a cpanel, the menu is most times arranged using the &lt;ul&gt;&lt;/ul&gt; tag in HTML format. For example;

	<ul class="menu-list">

		<!--- menu 1 --->
		
		<li class="menu-item">
			<a href='dashboard-link.php'>Dashboard</a>
		</li>
		
		<!--- menu 2 --->
		
		<li class="menu-item">
		
			<a href='#'>Account</a>
			
			<ul class="dropdown"> <!-- dropdown for account -->
			
				<li class="menu-item">
					<a href='the-activities.php'>Activities</a>
				</li>
					
				<li class="menu-item">
					<a href='javascript:void(0)'>Settings</a>
					<ul class="dropdown"> <!--- dropdown for settings --->
						<li class="menu-item">
							<a href='profile-settings.php'> Profile </a>
							<a href='password-settings.php'> Change password </a>
							<a href='preference-settings.php'> Change Preference </a>
						</li>
					</ul>
				</li>
				
			</ul>
		</li>
		
		<!--- menu 3 --->
		<li class="menu-item">
			<a href='logout-link.php'>Logout</a>
		</li>
		
	</ul>

Now, whenever you want to add a new menu to the list or you want to remove a menu from the list, you must edit the file that contains the menu.
This is not helpful especially when the menu needs to be replicated in different pages.
Like, you want "dashboard page" to show "Logout" as the last menu item.
But, you want "settings page" to show "Back to dashboard" as the last menu item.
Then, you will need to edit both files to change each menu items.

With menufy, things get a lot easier. The menufy class arranges your menu in an array format giving you an easier method to programmically add or remove any section of your menu without editing any single page.

How it works;

Using the HTML menu above as an example, we will create an equivalent using the menufy class

	$menu = new menufy();

	// Menu 1; - [depth=0]

	$menu->add("menu-1", array(
		"label" => "Dashboard",
		"link" => "dashboard-link.php"
	));


	// Menu 2; - [depth=0]

	$menu->add("menu-2", array(
		"label" => "Account"
	)); // if link is not set, link will default to '#'

		// submenu for: Menu 2 - [depth=1]
		
		$menu->add_submenu("menu-2", "submenu-1", array(
			"label" => "Activities",
			"link" => "the-activities.php"
		));

		$menu->add_submenu("menu-2", "submenu-2", array(
			"label" => "Settings",
			"link" => "javascript:void(0)"
		));

			// submenu for: Menu 2 > Submenu 2 - [depth=2]
			
			$menu->add_submenu("menu-2.submenu-2", "inner-menu-1", array(
				"label" => "Profile",
				"link" => "profile-settings.php"
			));

			$menu->add_submenu("menu-2.submenu-2", "inner-menu-2", array(
				"label" => "Change Password",
				"link" => "password-settings.php"
			));

			$menu->add_submenu("menu-2.submenu-2", "inner-menu-3", array(
				"label" => "Change Preference",
				"link" => "preference-settings.php"
			));
			

	// Menu 3; - [depth=0]

	$menu->add("menu-3", array(
		"label" => "logout",
		"link" => "logout-link.php"
	));


The above menu will be created an arranged in an array. Although, it doesn't do anything because it has not been added to the HTML page.

Next, On the section where the HTML Menu is suppose to appear, you write the following code;

	<ul class="menu-list">
		<?php $menu->enlist(null, function($data, $name, $menu) {
				ob_start();
			?>
				<li class="menu-item">
					<a href="<?php echo $data['link']; ?>"><?php echo $data['label']; ?></a>
					<?php if( !empty($data['submenu']) ): ?>
						<ul class="dropdown"><?php $menu->enlist($data['submenu']); ?></ul>
					<?php endif; ?>	
				</li>
			<?php 
				$menu_item = ob_get_clean();
				return $menu_item;
			});
		?>
	</ul>

The first parameter of enlist accepts a menu or submenu. "null" means, use the root menu [depth=0].

The function will list all menu in [depth=0]. Then,

	<?php if( !empty($data['submenu']) ): ?>
		<ul class="dropdown"><?php $menu->enlist($data['submenu']); ?></ul>
	<?php endif; ?>

The above function checks if there is a submenu and list them within the root menu.
This iteration continues to check for submenu's until all menu are listed.

The output however is based on what you returned in the function at parameter 2.

In all menu, label is required.
You can add any other data you want to the array you passed to the menu. However, depth & submenu will be overridden;
Now you can freely update or remove any menu at any time.

	// add submenu under menu-1
	
	$menu->add_submenu("menu-1", "submenu-name", array(
		"label" => "second level",
		"link" => "javascript:void(0)", // do nothing
		"depth" => 44, // will be overridden to 1,
		"submenu" => "crack" // will be overridden to an empty array,
		"class" => "my-custom-class",
		"info" => "other-custom-info"
	));
	
	// remove preference from submenu-2 
	
	$menu->detach("menu-2.submenu-2.inner-menu-3");
	

Try it! You're gonna love it.
