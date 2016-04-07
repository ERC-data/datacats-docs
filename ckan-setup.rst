CKAN setup
==========
Source install from github with virtualenvironment `project = ckan` and datacats `environment = aerdb`

Datacats installation
*********************
Activate your virtual environment, then follow datacats source install to ensure that you are using the most up to date version ::
  
  workon ckan
  git clone https://github.com/datacats/datacats.git upstream
  cd datacats
  python setup.py install
  datacats pull
  #Initialise as local git repository 
  git remote add origin https://github.com/SAEnergyData/datacats.git

Create datacats environment. The environment will live in ``/var/projects/ckan`` and the database in ``~/.datacats``::
  
  datacats create --ckan latest aerdb 
  datacats reload --production --address=xxx.xxx.xxx.xx --site-url http://energydata.uct.ac.za aerdb 80 

Some comments:

-   use ``--ckan latest`` to ensure you're using the most up-to-date version
-   must specify ``--production`` for the site to be available online

If you get an ``AttributeError`` when trying to log in to your site you can fix it with the following workaround https://github.com/ckan/ckan/commit/a4de57e5>::

  datacats shell
  pip freeze | grep lchemy
  pip install SQLAlchemy==0.9.6
  exit

CKAN Plugins
************
<span style="color:red">After installing plugins they must be installed and datacats must be reloaded:</span>::

  datacats install
  datacats reload

*************
ckanext-pages
*************
Add static pages to CKAN.::

  git clone git@github.com:ckan/ckanext-pages.git upstream
  cd aerdb/ckanext-pages/ckanext/pages    
  nano actions.py
  #CTRL+W new_authz ---> replace with authz
  #CTRL+X
  
  nano auth.py
  #CTRL+W new_authz ---> replace with authz
  #CTRL+X
  
  nano db.py
  #add following line to init_db() function pages_table
  extend_existing=True
  #Ctrl+X

Then add as [local repository to GitHub](#L0) to customise your fork.
*****************
ckanext-hierarchy
*****************
Enables organisational hierarchies to allow for parent and sub-organisations::

  git clone git@github.com:datagovuk/ckanext-hierarchy.git

*********************
ckanext-featuredviews
*********************

  git clone git@github.com:datacats/ckanext-featuredviews.git

*******************
ckanext-ytp-request
*******************

*Currently not used as there are several issues.*::

  git clone git@github.com:yhteentoimivuuspalvelut/ckanext-ytp-request

In the latest ckan version new\_authz has been replaced with authz. Must replace references.
Concerned files:
``/project/ckanext-ytp-request/ckanext/ytp/request/auth.py``
``/project/ckanext-ytp-request/ckanext/ytp/request/logic.py``

********************
ckanext-datarequests
********************
::

  git clone https://github.com/conwetlab/ckanext-datarequests.git upstream
  cd aerdb/ckanext-datarequests/ckanext/datarequests  
  nano db.py
  #add following line to init_db() function datarequests_table
  extend_existing=True
  #add following line to init_db() function comments_table
  extend_existing=True
  #Ctrl+X

Then add as [local repository to GitHub](#L0) to customise your fork.

**************
ckanext-disqus
**************

To use this extension:

1.  Create a Disqus account
2.  Add a site to your Disqus account for this CKAN
3.  Select 'Advanced' and add %(site\_url)s to trusted domains, e.g. energydata.uct.ac.za
4.  Add to config file: ``disqus.name = shortname``
::

  git clone https://github.com/ckan/ckanext-disqus.git

Username: UCTERC
Password: xxxxx
Change settings: https://aerdb.disqus.com/admin/settings/general/

****************************
Config file: development.ini
****************************
Remember to ``datacats reload`` after making changes to the config file.::
  
  ## Authorization Settings
  ckan.auth.anon_create_dataset = false
  ckan.auth.create_unowned_dataset = false
  ckan.auth.create_dataset_if_not_in_organization = false
  ckan.auth.user_create_groups = true
  ckan.auth.user_create_organizations = false
  ckan.auth.user_delete_groups = true
  ckan.auth.user_delete_organizations = false
  ckan.auth.create_user_via_api = true
  ckan.auth.create_user_via_web = true
  ckan.auth.roles_that_cascade_to_sub_groups = admin editor
  
  ## Search Settings
  ckan.site_id = default
  solr_url = http://solr:8080/solr
  
  #ckan.simple_search = 1
  
  ## CORS Settings
  # If cors.origin_allow_all is true, all origins are allowed.
  # If false, the cors.origin_whitelist is used.
  # ckan.cors.origin_allow_all = true
  # cors.origin_whitelist is a space separated list of allowed domains.
  # ckan.cors.origin_whitelist = http://example1.com http://example2.com
  
  ## Plugins Settings
  # Note: Add ``datastore`` to enable the CKAN DataStore
  #       Add ``datapusher`` to enable DataPusher
  #       Add ``resource_proxy`` to enable resorce proxying and get around the
  #       same origin policy
  ckan.plugins = datastore resource_proxy text_view datapusher recline_grid_view recline_graph_view aerdb_theme pages featuredviews hierarchy_display hierarchy_form datarequests disqus
  
  # Define which views should be created by default
  # (plugins must be loaded in ckan.plugins)
  ckan.views.default_views = image_view text_view recline_view
  
  # ckanext-disqus
  disqus.name = aerdb
  
  # ckanext-datarequests
  ckan.datarequests.comments = true
  
  ## Front-End Settings
  ckan.site_title = African Energy Research Database
  ckan.site_logo =
  ckan.site_description =
  ckan.favicon = /images/icons/ckan.ico
  ckan.gravatar_default = identicon
  ckan.preview.direct = png jpg gif
  ckan.preview.loadable = html htm rdf+xml owl+xml xml n3 n-triples turtle plain atom csv tsv rss txt json
  ckan.display_timezone = server
  
  # package_hide_extras = for_search_index_only
  #package_edit_return_url = http://another.frontend/dataset/<NAME>
  #package_new_return_url = http://another.frontend/dataset/<NAME>
  #ckan.recaptcha.version = 1
  #ckan.recaptcha.publickey =
  #ckan.recaptcha.privatekey =
  #licenses_group_url = http://licenses.opendefinition.org/licenses/groups/ckan.json
  # ckan.template_footer_end =
  
  ## Internationalisation Settings
  ckan.locale_default = en
  ckan.locale_order = en pt_BR ja it cs_CZ ca es fr el sv sr sr@latin no sk fi ru de pl nl bg ko_KR hu sa sl lv
  ckan.locales_offered =
  ckan.locales_filtered_out = en_GB
  
  ## Feeds Settings
  ckan.feeds.authority_name =
  ckan.feeds.date =
  ckan.feeds.author_name =
  ckan.feeds.author_link =
  
  ## Storage Settings
  ckan.storage_path = /var/www/storage
  #ckan.max_resource_size = 10
  #ckan.max_image_size = 2
  
  ## Datapusher settings
  # Make sure you have set up the DataStore
  #ckan.datapusher.formats = csv xls xlsx tsv application/csv application/vnd.ms-excel application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
  ckan.datapusher.url = http://datapusher:8800
  
  # Resource Proxy settings
  # Preview size limit, default: 1MB
  #ckan.resource_proxy.max_file_size = 1048576
  # Size of chunks to read/write.
  #ckan.resource_proxy.chunk_size = 4096
  
  ## Activity Streams Settings
  ckan.activity_streams_enabled = true
  ckan.activity_list_limit = 31
  ckan.activity_streams_email_notifications = true
  ckan.email_notifications_since = 2 days
  ckan.hide_activity_from_users = %(ckan.site_id)s
  
  ## Email settings
  email_to = *****
  error_email_from = *****
  smtp.server = smtp.gmail.com:587
  smtp.starttls = True
  smtp.user = *****
  smtp.password = *****
  smtp.mail_from = *****
  
  ## Logging configuration
  [loggers]
  keys = root, ckan, ckanext
  
  [handlers]
  keys = console
  
  [formatters]
  keys = generic
  
  [logger_root]
  level = WARNING
  handlers = console
  
  [logger_ckan]
  level = INFO
  handlers = console
  qualname = ckan
  propagate = 0
  
  [logger_ckanext]
  level = DEBUG
  handlers = console
  qualname = ckanext
  propagate = 0
  
  [handler_console]
  class = StreamHandler
  args = (sys.stderr,)
  level = NOTSET
  formatter = generic
  
  [formatter_generic]
  format = %(asctime)s %(levelname)-5.5s [%(name)s] %(message)s
  ```
  
  ### Config theme customisation
  
  Add the project theme directory to github for easy management
  
  ``` r
  cd /var/projects/ckan/aerdb/ckanext-aerdbtheme
  touch .gitignore #create .gitignore file
  nano gitignore 
  #paste the following code in .gitignore and Ctrl^X
  *.pyc
  ckanext_aerdbtheme.egg-info/*
  build/*
  dist/*

Then add as [local repository to GitHub](#L0)
