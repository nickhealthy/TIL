# Django_project



### Activity log

* Django Project settings
  1. `Django` connect with remote `MySQL` setting
  2. Set `venv`[(Virtual Environments and Packages)][https://docs.python.org/3/tutorial/venv.html]
* Django: Adding songs to DB
* Django: To make SONG, ARTIST SEARCH function(Team member: baejinsoo)
* Git settings [GitURL][https://github.com/multicampus-cloud/django_playlist]
* Today I learnd: Django how to MAKE and OPERATE  `Form.py`
* Dicide next plan with project team



### Django Project settings

* `Django` connect with remote `MySQL` setting

  1. Create new **account** in `MySQL`

  2. Change django_settings.py: *DATABASES*, *ALLOWED_HOSTS*

     * ```python
       DATABASES = {
           'default': {
           'ENGINE': 'django.db.backends.mysql',
           'NAME': 'project_db', # DB명
           'USER': 'project', # 데이터베이스 계정
           'PASSWORD':'project', # 계정 비밀번호
           'HOST':'172.30.1.31', # 데이테베이스 IP
           'PORT':'3306', # 데이터베이스 port
           'OPTIONS': {
                   'charset': 'utf8'  # This is the important line
                   }
           }
       }
       ```

     * ```python
       ALLOWED_HOSTS = ['*']
       ```

  

* Set `venv`[(Virtual Environments and Packages)][https://docs.python.org/3/tutorial/venv.html]

  1. Check the **version** you are using `Python` in the `IDE`

     * As the first configuration, you need to configure the official Python module.

  2. Enter this **command** in the command window or other OS

     * ```python
       python -m venv venv
       ```

     * Then the venv folder is created. Based on the Python version currently in use

  3. Install the version control file(requirements.txt) used by the project through the command

     * ```python
       pip install -r requirements.txt
       ```

     * Then all modules are installed.



### Todo List(Future plans)

* To make TAG, GENRE SEARCH function(Team member: baejinsoo)
* To make WEB CRAWL for to get photos(Me)
* To modify UI(Team member: ok2qw66)