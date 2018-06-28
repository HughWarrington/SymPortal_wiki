To setup a local instance of the SymPortal analytical framework follow the directions below. The directions are aimed at getting SymPortal running using a MacOS or linux environment. However, these should be applicable to getting SymPortal running on a Window OS with only minor modifications.

#### 1. Clone the SymPortal repository into your chosen working directory.

See GitHub's [online documentation](https://help.github.com/articles/cloning-a-repository/) for further details.

#### 2. Configure settings.py

You will find a file in your chosen directory named: **settings_blank.py**.

Rename this file **settings.py**.

Generate a bespoke secret key [here](https://www.miniwebtool.com/django-secret-key-generator/) and paste it into your settings.py file so that:

`SECRET_KEY = ''` 

becomes 

`SECRET_KEY = '^3#q(9(ky$5#=tg$8m45k*5gh!==n&nh)5*8=hf0g)giob^mzd'` (key only for example)

Save the modified **settings.py**.


