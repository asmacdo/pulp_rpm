Configuration
=============

Protected Repositories
----------------------

Repository authentication allows the creation of *protected* repositories in the
Pulp server. Consumers attempting to access protected repositories with yum
operations require some form of authentication in order to be granted access.

Two configuration file changes are necessary to enable repository authentication.

* Edit ``/etc/pulp/server.conf`` and set the ``ssl_ca_certificate`` option to
  the full path of the CA certificate that signed the Pulp server's httpd SSL certificate.
  If this option is not set, it will default to ``/etc/pki/pulp/ssl_ca.crt``.
  This file must be readable by the apache user.

.. note::
  If the default self signed certificate that is generated when mod_ssl
  is installed is being used as the Pulp server's certificate, copying that certificate
  to ``/etc/pki/pulp/ssl_ca.crt`` and making it apache readable will suffice.
  The default location for that certificate is ``/etc/pki/tls/certs/localhost.crt``
  or ``/etc/pki/tls/certs/<hostname>.crt``.

* Edit ``/etc/pulp/repo_auth.conf`` and set the ``enabled`` option to ``true``.
  Save the file and restart Apache.

Global Repo Authentication
^^^^^^^^^^^^^^^^^^^^^^^^^^

Repository authentication may be configured globally for all repositories in the
Pulp server or individually on a per repo basis. In the event that both are specified,
only the individual repository authentication check will take place.

Global repository authentication is enabled by placing the authentication
credentials under ``/etc/pki/pulp/content/``. The following files are relevant:

``pulp-global-repo.ca``
  CA certificate used to validate inbound consumer certificates. If the consumer's
  certificate cannot be validated by this CA, the consumer is automatically
  rejected as being unauthorized.

``pulp-global-repo.cert``
  Certificate to provide to consumers when they bind to repositories. If a
  repository overrides global repository authentication at the repository level,
  the certificate provided for the repository itself is used in place of this
  file. This file is optional; if unspecified, bound consumers will need to
  acquire a valid certificate for accessing the repository through other means.

``pulp-global-repo.key``
  If the private key for the consumer certificate above is not included in the
  certificate itself, it may be located in this file and will be sent to
  bound consumers at the same time as the certificate.


Individual Repository Authentication
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Individual repositories can be setup to do SSL authentication. This allows you
to use authentication on only specific repositories while leaving others
unprotected, or to have different credentials for some repositories than others.

The three certificates listed above can be passed to the repository ``create``
or ``update`` command using the following options respectively:

* ``--feed-ca-cert``
* ``--feed-cert``
* ``--feed-key``

