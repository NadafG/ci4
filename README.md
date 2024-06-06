CodeIgniter 4 Filters Guide
===========================

Introduction
------------
In CodeIgniter 4, filters are used to perform tasks either before or after the execution of a request. Filters are similar to middleware in other frameworks and can be used for a variety of purposes, such as authentication, logging, input validation, and more.

Creating a Filter
-----------------
First, you need to create a filter class. Filters in CodeIgniter 4 should implement the `CodeIgniter\Filters\FilterInterface`.

Step 1: Create a Filter Class
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Create a new filter class in `app/Filters/`.

.. code-block:: php

    <?php

    namespace App\Filters;

    use CodeIgniter\HTTP\RequestInterface;
    use CodeIgniter\HTTP\ResponseInterface;
    use CodeIgniter\Filters\FilterInterface;

    class AuthFilter implements FilterInterface
    {
        public function before(RequestInterface $request, $arguments = null)
        {
            // Perform your authentication check here
            if (! session()->get('isLoggedIn')) {
                // Redirect to login page if the user is not authenticated
                return redirect()->to('/login');
            }
        }

        public function after(RequestInterface $request, ResponseInterface $response, $arguments = null)
        {
            // Code to execute after the request
        }
    }

Registering the Filter
----------------------
Next, you need to register the filter in `app/Config/Filters.php`.

Step 2: Update Filters Configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Edit `app/Config/Filters.php` to register your custom filter.

.. code-block:: php

    <?php

    namespace Config;

    use CodeIgniter\Config\BaseConfig;

    class Filters extends BaseConfig
    {
        public $aliases = [
            'csrf'      => \CodeIgniter\Filters\CSRF::class,
            'toolbar'   => \CodeIgniter\Filters\DebugToolbar::class,
            'honeypot'  => \CodeIgniter\Filters\Honeypot::class,
            'auth'      => \App\Filters\AuthFilter::class, // Register your filter here
        ];

        public $globals = [
            'before' => [
                // 'honeypot',
                // 'csrf',
                // 'auth' // Apply globally if needed
            ],
            'after' => [
                'toolbar',
                // 'honeypot'
            ],
        ];

        public $methods = [
            'post' => ['csrf'],
        ];

        public $filters = [
            'auth' => ['before' => ['admin/*']], // Apply to specific routes
        ];
    }

Applying Filters
----------------
You can apply filters globally, to specific HTTP methods, or to specific routes.

Global Filters
~~~~~~~~~~~~~~
Global filters are applied to all requests. Update the `$globals` array in `app/Config/Filters.php`.

.. code-block:: php

    public $globals = [
        'before' => [
            'auth', // Apply globally
        ],
        'after' => [
            'toolbar',
        ],
    ];

Method Filters
~~~~~~~~~~~~~~
Filters can be applied to specific HTTP methods.

.. code-block:: php

    public $methods = [
        'post' => ['csrf'], // Apply CSRF filter to all POST requests
    ];

Route-Specific Filters
~~~~~~~~~~~~~~~~~~~~~~
Filters can be applied to specific routes. Update the `$filters` array in `app/Config/Filters.php`.

.. code-block:: php

    public $filters = [
        'auth' => ['before' => ['admin/*']], // Apply to routes that match 'admin/*'
    ];

Using Filters in Routes
~~~~~~~~~~~~~~~~~~~~~~~
You can also define filters directly in your routes configuration.

Step 3: Apply Filters in Routes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Edit `app/Config/Routes.php` to apply filters to specific routes.

.. code-block:: php

    $routes->group('admin', ['filter' => 'auth'], function($routes) {
        $routes->get('dashboard', 'AdminController::dashboard');
        $routes->get('profile', 'AdminController::profile');
    });

Example of a Custom Filter
--------------------------
Let's say you have a simple authentication filter. Here’s how you would implement it.

AuthFilter Class
~~~~~~~~~~~~~~~~
Create `app/Filters/AuthFilter.php`:

.. code-block:: php

    <?php

    namespace App\Filters;

    use CodeIgniter\HTTP\RequestInterface;
    use CodeIgniter\HTTP\ResponseInterface;
    use CodeIgniter\Filters\FilterInterface;

    class AuthFilter implements FilterInterface
    {
        public function before(RequestInterface $request, $arguments = null)
        {
            // Check if user is logged in
            if (! session()->get('isLoggedIn')) {
                // If not logged in, redirect to login page
                return redirect()->to('/login');
            }
        }

        public function after(RequestInterface $request, ResponseInterface $response, $arguments = null)
        {
            // Code to execute after request is processed
        }
    }

Register the Filter
~~~~~~~~~~~~~~~~~~~
Update `app/Config/Filters.php`:

.. code-block:: php

    public $aliases = [
        'auth' => \App\Filters\AuthFilter::class,
    ];

    public $globals = [
        'before' => [
            // 'auth', // Uncomment to apply globally
        ],
        'after' => [
            'toolbar',
        ],
    ];

    public $filters = [
        'auth' => ['before' => ['admin/*']],
    ];

Apply in Routes
~~~~~~~~~~~~~~~
Update `app/Config/Routes.php`:

.. code-block:: php

    $routes->group('admin', ['filter' => 'auth'], function($routes) {
        $routes->get('dashboard', 'AdminController::dashboard');
        $routes->get('profile', 'AdminController::profile');
    });

Conclusion
----------
Filters in CodeIgniter 4 provide a flexible and powerful way to handle common tasks such as authentication, logging, and input validation. By creating custom filters and applying them appropriately, you can ensure your application remains clean and modular.
