RH Cloud(Remote Server) Project setup
=====================================


    Create an account in https://www.openshift.com/


    Enter as root user


**1 . Install RHC in local system**  
`sudo gem install rhc`  
`~# rhc` 

    Usage: rhc [--help] [--version] [--debug] <command> [<args>]  
    Command line interface for OpenShift.
    Getting started:  
    setup              Connects to OpenShift and sets up your keys and domain  
    create-app         Create an application  
    apps               List all your applications  
    cartridges         List available cartridges  
    add-cartridge      Add a cartridge to your application  
    server             Display information about the status of the OpenShift service.  
    logout             End the current session  

**2 . Setup RHC in Local Machine :**

`~# rhc setup -l karthikkumar@gmail.com` (karthikkumar@gmail.com is a registered email in RHC Account)  
`Password: *********`  

**3 . Create new application :**  

`~# rhc app create -a python -t python-2.6` ( Here python is an application name )  

    Application Options
    -------------------
    Namespace:  capsonenews
    Cartridges: python-2.6
    Gear Size:  default
    Scaling:    no
    Creating application 'python' ... done
    Waiting for your DNS name to be available ... done
    Downloading the application Git repository ...
    Cloning into 'python'...
    The authenticity of host 'python-capsonenews.rhcloud.com (54.221.190.192)' can't be established.
    RSA key fingerprint is cf:ee:77:cb:0e:fc:02:d7:72:7e:ae:80:c0:90:88:a7.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'python-capsonenews.rhcloud.com,54.221.190.192' (RSA) to the list of known hosts.
    Your application code is now in 'python'

    python @ http://python-capsonenews.rhcloud.com/ (uuid: 52831755500446cf2d0007b3)
    --------------------------------------------------------------------------------
    Created: 11:38 AM
    Gears:   1 (defaults to small)
    Git URL: ssh://52831755500446cf2d0007b3@python-capsonenews.rhcloud.com/~/git/python.git/
    SSH:     52831755500446cf2d0007b3@python-capsonenews.rhcloud.com

    python-2.6 (Python 2.6)
    -----------------------
    Gears: 1 small

    RESULT:
    Application python was created.

**4 .**  
`~# cd python/`  

**5 .**
`~/python# ls`

    app.py.disabled  data  libs  README.md  setup.py  setup.pyc  setup.pyo  wsgi

`~/python# vi setup.py`

    from setuptools import setup
    setup(name='YourAppName',
    version='1.0',
    description='OpenShift App',
    author='Your Name',
    author_email='example@example.com',
    url='http://www.python.org/sigs/distutils-sig/',
    install_requires=['Django>=1.5','beautifulsoup4'],
    )

add required application need to be install. It should be given install_requires (eg : pip install beautifulsoup4)

**6 : You can access server by ssh and username is mention in above application**

`~/python# ssh 52831755500446cf2d0007b3@python-capsonenews.rhcloud.com`

    [python-capsonenews.rhcloud.com 52831755500446cf2d0007b3]\>exit

**7 : Navigate to wsgi directory**

`~/python# cd wsgi/`  
`~/python/wsgi# ls`  

    application  static

**8 : Copy the python project into the wsgi folder**

`~/python/wsgi# cp -R /home/capsone-system7/Karthik/feedparser ./`

**9 : Remove the "application" file inside wsgi and create new application file then store the details into it.**

`~/python/wsgi# rm -rf application`
`~/python/wsgi# vi application`

Add this details into application file (for more info : https://gist.github.com/ekarthikkumar/8283468 )

    #!/usr/bin/python
    import os, sys

    os.environ['DJANGO_SETTINGS_MODULE'] = 'PROJECTNAME.settings'
    sys.path.append(os.path.join(os.environ['OPENSHIFT_REPO_DIR'], 'wsgi',
    'PROJECTNAME'))

    virtenv = os.environ['APPDIR'] + '/virtenv/'
    os.environ['PYTHON_EGG_CACHE'] = os.path.join(virtenv, 'lib/python2.6/site-packages')
    virtualenv = os.path.join(virtenv, 'bin/activate_this.py')

    try:
        execfile(virtualenv, dict(__file__=virtualenv))
    except IOError:
        pass

    #
    # IMPORTANT: Put any additional includes below this line.  If placed above this
    # line, it's possible required libraries won't be in your searchable path

    from django.core.handlers import wsgi
    application = wsgi.WSGIHandler()

**10 : Navigate to python folder then push project to remote(rhc) server**

`~/python# git status`  
`~/python# git add .`  
`~/python# git commit -a -m "commit message"`  
`~/python# git push`  

**11 : Now project is in live**

`http://python-capsonenews.rhcloud.com`

**12 : To see your apps which is in server**

`rhc apps`

    python @ http://python-capsonenews.rhcloud.com/ (uuid: 52831755500446cf2d0007b3)
    --------------------------------------------------------------------------------
    Created: 11:38 AM
    Gears:   1 (defaults to small)
    Git URL: ssh://52831755500446cf2d0007b3@python-capsonenews.rhcloud.com/~/git/python.git/
    SSH:     52831755500446cf2d0007b3@python-capsonenews.rhcloud.com

    python-2.6 (Python 2.6)
    -----------------------
    Gears: Located with mysql-5.1

    mysql-5.1 (MySQL 5.1)
    ---------------------
    Gears:          Located with python-2.6
    Connection URL: mysql://$OPENSHIFT_MYSQL_DB_HOST:$OPENSHIFT_MYSQL_DB_PORT/
    Database Name:  python
    Password:       Dai8T_Teb8l_
    Username:       adminXJb7TRE

    You have 1 applications

**11 : To delete the project in server**  

`rhc app delete python` (Here python is an app name)

    This is a non-reversible action! Your application code and data will be permanently deleted if you continue!

    Are you sure you want to delete the application 'python'? (yes|no): yes

    Deleting application 'python' ... deleted

**12 : rhc apps**

    No applications. Use 'rhc app create'.
