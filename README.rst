****************************************
django simple audit
****************************************
This applications was created to audit model's changes and maintain a log of them


Installation
===============
You can install django-simple-audit in 2 ways: using pip or by setup.py install

.. code-block:: bash

    $ pip install django-simple-audit


Then modify your settings.py, adding the package `simple_audit` in INSTALLED_APPS and in MIDDLEWARE_CLASSES add
`simple_audit.middleware.TrackingRequestOnThreadLocalMiddleware`:

.. code-block:: bash

	INSTALLED_APPS = (
	    '...',
	    'simple_audit',
	)

	MIDDLEWARE_CLASSES = (
	     '...',
	     'simple_audit.middleware.TrackingRequestOnThreadLocalMiddleware',
	)


Usage
===============

Tracking changes on a model
----------------------------

to audit a model you need import `simple_audit` and then register the model to be audited.

.. code-block:: python

	from django.db import models
	import simple_audit


	class Message(models.Model):

	    title = models.CharField(max_length=50, blank=False)
	    text = models.TextField(blank=False)

	    def __unicode__(self):
	        return self.text


	class Owner(models.Model):

	    name = models.CharField(max_length=50, blank=False)

	    def __unicode__(self):
	        return self.name


	class VirtualMachine(models.Model):

	    name = models.CharField(max_length=50, blank=False)
	    cpus = models.IntegerField()
	    owner = models.ForeignKey(Owner)
	    so = models.CharField(max_length=100, blank=False)
	    started = models.BooleanField()

	    def __unicode__(self):
	        return self.name


	simple_audit.register(Message, Owner, VirtualMachine)

Advanced Usage (without httprequest or our middleware)
--------------------------------------------------------

You can use django-simple-audit without an http request (for example in management command). In this situation
there is no http request on thread context. To ensure gathering all modification on a single AuditRequest, you can
specify it:

.. code-block:: python

	AuditRequest.new_request(path, user, ip)
	try:
	    # my code... in same thread
	finally:
	    AuditRequest.cleanup_request()



Dependencies
============

* Django >= 1.4.x
* django.contrib.contenttypes installed in INSTALLED_APPS


TODO
====
* Improve tests
* Audit changes in ManyToMany fields
