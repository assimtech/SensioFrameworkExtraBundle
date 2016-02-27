@Template
=========

Usage
-----

The ``@Template`` annotation associates a controller with a template name::

    use Sensio\Bundle\FrameworkExtraBundle\Configuration\Template;

    /**
     * @Template("SensioBlogBundle:Post:show.html.twig")
     */
    public function showAction($id)
    {
        // get the Post
        $post = ...;

        return array('post' => $post);
    }

When using the ``@Template`` annotation, the controller should return an
array of parameters to pass to the view instead of a ``Response`` object.

.. note::

    If you want to stream your template, you can make it with the following configuration::

        /**
         * @Template(isStreamable=true)
         */
        public function showAction($id)
        {
            // ...
        }


.. tip::
   If the action returns a ``Response`` object, the ``@Template`` 
   annotation is simply ignored.

If the template is named after the controller and action names, which is the
case for the above example, you can even omit the annotation value::

    /**
     * @Template
     */
    public function showAction($id)
    {
        // get the Post
        $post = ...;

        return array('post' => $post);
    }

Engine selection
^^^^^^^^^^^^^^^^

The default engine can be configured for your application. See
:ref:`Configuration <frameworkextra-configuration>`

You can override the default templating engine for a specific action::

    /**
     * @Template(engine="php")
     */
    public function showAction($id)
    {
        // ...
    }

If you are supporting multiple ``engines`` based on input ``_format`` you can
configure a default per ``_format``. This will override the ``deafult_engine``.

For example, if you wanted to::

* Leverage `fputcsv`_ for ``csv`` responses
* Control the view variables with ``php`` for ``json`` responses

.. configuration-block::

    .. code-block:: yaml

        sensio_framework_extra:
            # Other options...
            view:
                engines:
                    csv: php
                    json: php

    .. code-block:: xml

        <!-- xmlns:sensio-framework-extra="http://symfony.com/schema/dic/symfony_extra" -->
        <sensio-framework-extra:config>
            <!-- Other options... -->
            <view annotations="true" default_engine="twig">
                <view-engines>
                    <view-engine format="csv" engine="php" />
                    <view-engine format="json" engine="php" />
                </view-engines>
            </view>
        </sensio-framework-extra:config>

    .. code-block:: php

        // load the profiler
        $container->loadFromExtension('sensio_framework_extra', array(
            // Other options...
            'view' => array(
                'annotations'       => true,
                'default_engine'    => 'twig',
                'engines'           => array(
                    'csv'   => 'php',
                    'json'  => 'php',
                ),
            ),
        ));

You can also override the default templating engine for a specific ``_format``
per action::

    /**
     * @Template(engines={
     *      csv="php",
     *      json="php",
     * })
     */
    public function listAction()
    {
        // ...
    }

Template variables from ParamConverter
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

And if the only parameters to pass to the template are method arguments, you
can use the ``vars`` attribute instead of returning an array. This is very
useful in combination with the ``@ParamConverter`` :doc:`annotation
<converters>`::

    /**
     * @ParamConverter("post", class="SensioBlogBundle:Post")
     * @Template("SensioBlogBundle:Post:show.html.twig", vars={"post"})
     */
    public function showAction(Post $post)
    {
    }

which, thanks to conventions, is equivalent to the following configuration::

    /**
     * @Template(vars={"post"})
     */
    public function showAction(Post $post)
    {
    }

You can make it even more concise as all method arguments are automatically
passed to the template if the method returns ``null`` and no ``vars``
attribute is defined::

    /**
     * @Template
     */
    public function showAction(Post $post)
    {
    }

.. _`fputcsv`: http://php.net/manual/en/function.fputcsv.php