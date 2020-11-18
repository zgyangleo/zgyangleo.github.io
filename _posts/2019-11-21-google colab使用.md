---
layout: post
title:  "google colab使用"
date:   2019-11-21 16:39:01 +0800
categories: Colab
tag: Colab
---

* content
{:toc}


        !apt-get install -y -qq software-properties-common python-software-properties module-init-tools
        !add-apt-repository -y ppa:alessandro-strada/ppa 2>&1 > /dev/null
        !apt-get update -qq 2>&1 > /dev/null
        !apt-get -y install -qq google-drive-ocamlfuse fuse
        from google.colab import auth
        auth.authenticate_user()
        from oauth2client.client import GoogleCredentials
        creds = GoogleCredentials.get_application_default()
        import getpass
        !google-drive-ocamlfuse -headless -id={creds.client_id} -secret={creds.client_secret} < /dev/null 2>&1 | grep URL
        vcode = getpass.getpass()
        !echo {vcode} | google-drive-ocamlfuse -headless -id={creds.client_id} -secret={creds.client_secret}


        from google.colab import drive
        drive.mount('/content/gdrive')


        import os
        os.chdir("/content/gdrive/My Drive")


