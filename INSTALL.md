This documentation is very out-of-date. Follow it approximately. Be
aware:

* scripts/lmsanalytics and scripts/edxanalytics for starting
  servers (new addition). 
* The mitx repository was renamed to edx-platform for the open source
  release.



Installing the minimal working analytics configuration
-----

First, decide on your directories:
* VIRTUALENV_DIR = directory where you create your python virtualenv.
* BASE_DIR = directory you start in before cloning the analytics repo. (so something like /home/bob)
* EDINSIGHTS_DIR = directory where the edinsights repo is cloned. (so something like /home/bob/edinsights/)
* EDXANALYTICS_DIR = directory where the edxanalytics repo is cloned (so something like /home/bob/edxanalytics/)

Then, start to install:
Step 1. Install edinsights

    cd BASE_DIR
    git clone git@github.com:MITx/edinsights.git
    cd edinsights (this is the EDINSIGHTS_DIR)
    sudo xargs -a apt-packages.txt apt-get install
    sudo apt-get remove python-virtualenv python-pip
    sudo easy_install pip
    pip install virtualenv
    mkdir VIRTUALENV_DIR
    virtualenv VIRTUALENV_DIR
    source VIRTUALENV_DIR/bin/activate
    python setup.py install

Step 2. Install djeventstream

    cd BASE_DIR
    git clone git@github.com:edx/djeventstream.git
    cd djeventstream
    python setup.py install
  
Step 3. Install edxanalytics

    cd BASE_DIR
    git clone git@github.com:MITx/edxanalytics.git
    cd edxanalytics (this is the EDXANALYTICS_DIR)
    sudo xargs -a apt-packages.txt apt-get install
    pip install -r requirements.txt
    mkdir BASE_DIR/db
    cd EDXANALYTICS_DIR/src/edxanalytics
    
Note: Ensure that IMPORT_EDX_MODULES in edxanalytics/settings.py is False .

    python manage.py syncdb --database=remote --settings=edxanalytics.settings --pythonpath=. (this may fail, but that is fine)
    python manage.py syncdb --database=default --settings=edxanalytics.settings --pythonpath=.
    mkdir EDXANALYTICS_DIR/staticfiles
    python manage.py collectstatic --settings=edxanalytics.settings --noinput --pythonpath=.

Step 4. Install edxdataanalytic to connect to the edX lms

    cd EDXANALYTICS_DIR/src/edxdataanalytic
    python setup.py install

Step 5. Now run two servers: edxanalytics and lms

    cd EDXANALYTICS_DIR/scripts
    ./edxanalytics.sh 
    ./lmsanalytics.sh 

Note: To manually run the edxanalytics server:

    python manage.py runserver 127.0.0.1:9022 --settings=edxanalytics.settings --pythonpath=. --nostatic

Step 6. Navigate to 127.0.0.1:9022 (or any port that you are running edxanalytics with) in your browser. 
You should see a login screen.

Step 7. Create a user for login

    cd EDXANALYTICS_DIR/src/edxanalytics
    python manage.py shell --settings=edxanalytics.settings --pythonpath=.
    
From the shell, run the following:

    from django.contrib.auth.models import User
    user = User.objects.create_user("test","test@test.com","test")
    
Now you should be able to log in, and start using the edxanalytics modules.

If you are using the aws settings (i.e., deploying):
-----

* MITX_DIR = directory where you clone MITX

Follow the steps below:

    cd EDXANALYTICS_DIR/src/edxanalytics
    source VIRTUALENV_DIR/bin/activate
    python manage.py createcachetable django_cache --database=default --settings=edxanalytics.settings --pythonpath=.
    cd BASE_DIR
    git clone git@github.com:MITx/mitx.git
    cd MITX_DIR
    sudo xargs -a apt-packages.txt apt-get install
    If the above step does not work, remove npm and nodejs from apt-packages.txt
    pip install -r pre-requirements.txt
    pip install -r requirements.txt
    pip install webob
    pip install -r local-requirements.txt


Ignore
-----

Assorted notes; setting up a new machine with new edx/dj split. Below will turn into documentation

adduser pmitros
Create .ssh, and copy keys, set up sudoers

apt-get install emacs23 git python-pip python-matplotlib python-scipy mongodb apache2-utils python-mysqldb subversion ipython 
pip install django celery pymongo fs mako requests decorator South django-celery celery-with-redis

Nominally: 
pip install -e git+https://github.com/edx/djeventstream.git#egg=djeventstream

In practice, setup.py install

apt-get install nginx redis-server libmysqlclient-dev 
