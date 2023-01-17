## Build Driver Image

```dockerfile
CMD ["bash"]
RUN apt update  \
        && apt remove -y libcurl4  \
        && apt install -y apt-transport-https ca-certificates tzdata locales libcurl4 curl gnupg  \
        && DEBIAN_FRONTEND=noninteractive apt -y upgrade  \
        && echo ttf-mscorefonts-installer msttcorefonts/accepted-mscorefonts-eula select true | debconf-set-selections  \
        && echo 'UTC' | tee /etc/timezone  \
        && dpkg-reconfigure -f noninteractive tzdata  \
        && echo "gtk-cursor-blink=0" > /root/.gtkrc-2.0  \
        && apt update  \
        && apt install -y ttf-mscorefonts-installer ttf-dejavu-core fontconfig fontconfig-config fonts-dejavu-core fonts-liberation fonts-ubuntu-font-family-console fonts-wqy-zenhei fonts-thai-tlwg-ttf fonts-ipafont-mincho fonts-sahadeva fonts-noto-unhinted fonts-noto-color-emoji libfontconfig1 libfontenc1 libfreetype6 libxfont2 libxft2 libnss3-tools xfonts-base xfonts-encodings xfonts-utils flashplugin-installer xvfb pulseaudio fluxbox x11vnc feh wmctrl libnss-wrapper xsel  \
        && mkdir -p /var/lib/locales/supported.d/  \
        && grep UTF-8 /usr/share/i18n/SUPPORTED > /var/lib/locales/supported.d/all  \
        && locale-gen  \
        && update-locale  \
        && fc-cache -f -v  \
        && adduser --system --home /home/selenium --uid 4096 --ingroup root --disabled-password --shell /bin/bash selenium  \
        && mkdir -p /home/selenium/Downloads  \
        && mkdir -p /home/selenium/.fluxbox  \
        && chgrp -R 0 /home/selenium  \
        && chmod -R g=u /home/selenium  \
        && apt-get clean  \
        && rm -Rf /tmp/*  \
        && rm -Rf /var/lib/apt/lists/*
COPY dir:92cfad7c2e4eb5578be8d90a47e87eb920dd1b22461c78503bd8fb11e763573f in /usr/share/fluxbox/styles/
        usr/

COPY --chown=selenium:rootdir:92cfad7c2e4eb5578be8d90a47e87eb920dd1b22461c78503bd8fb11e763573f in /home/selenium/.fluxbox/
        home/
        home/selenium/
        home/selenium/.fluxbox/
        home/selenium/.fluxbox/aerokube
        home/selenium/.fluxbox/init

COPY file:5eca0c25d5a63289deb7057a7376da47cd61a57e4e6e95d0356bc9025b3c96f1 in /usr/share/images/fluxbox/
        usr/

COPY file:0070c1e0299d6bcea5017d3994d98e087a404719725bc57dd57ed74c0591b588 in /usr/bin/
        usr/
        usr/bin/
        usr/bin/fileserver

COPY file:df1f9afbe5530547789bf7ccd5f395aeb2b7b15295e34b182cc2c8f9debbd1f9 in /usr/bin/
        usr/
        usr/bin/
        usr/bin/xseld

ARG VERSION
ARG PACKAGE=microsoft-edge-beta
ARG INSTALL_DIR=msedge-beta
LABEL browser=microsoft-edge-beta:94.0.992.19-1
|3 INSTALL_DIR=msedge-beta PACKAGE=microsoft-edge-beta VERSION=94.0.992.19-1 RUN curl -s https://packages.microsoft.com/keys/microsoft.asc | apt-key add -  \
        && echo 'deb [arch=amd64] https://packages.microsoft.com/repos/edge stable main' > /etc/apt/sources.list.d/microsoft-edge.list  \
        && apt-get update  \
        && apt-get -y --no-install-recommends install $PACKAGE=$VERSION  \
        && sed -i -e 's@exec -a "$0" "$HERE/msedge"@& --no-sandbox --disable-gpu@' /opt/microsoft/$INSTALL_DIR/$PACKAGE  \
        && chown root:root /opt/microsoft/$INSTALL_DIR/msedge-sandbox  \
        && chmod 4755 /opt/microsoft/$INSTALL_DIR/msedge-sandbox  \
        && microsoft-edge --version  \
        && rm -Rf /tmp/*  \
        && rm -Rf /var/lib/apt/lists/*
ENV DBUS_SESSION_BUS_ADDRESS=/dev/null

COPY file:663b3fcf68e26e5f456e9d870932d65d40a0ba251d1d8b62ec794fea0c893108 in /usr/bin/
        usr/
        usr/bin/
        usr/bin/msedgedriver

COPY file:e4e074e4a9fc4986ffc454167aca5f929fea6587c4b646d9a011d7742a82fc3d in /
        entrypoint.sh

RUN chmod +x /usr/bin/msedgedriver
USER selenium
EXPOSE 4444
ENTRYPOINT ["/entrypoint.sh"]
LABEL driver=msedgedriver:94.0.992.19
USER [root]
COPY file:0055a1ddbc4aee876f2df61a67683527587e658723e455f1622d9277d35a51f3 in /usr/local/share/ca-certificates/
        usr/
        usr/local/
        usr/local/share/
        usr/local/share/ca-certificates/
        usr/local/share/ca-certificates/neweggqa.crt

COPY file:0ed20efe4a88c84e636b323de08e46111080d670a64f922257a198d5c09480b1 in /usr/local/share/ca-certificates/
        usr/
        usr/local/
        usr/local/share/
        usr/local/share/ca-certificates/
        usr/local/share/ca-certificates/mcafee.crt

COPY file:ae1e6e9c234a16a6ffa1ad92d287a5121fb67bb605d5cb8e2d413a491a47227a in /usr/local/share/ca-certificates/
        usr/
        usr/local/
        usr/local/share/
        usr/local/share/ca-certificates/
        usr/local/share/ca-certificates/NeweggRootG2.crt

COPY file:5c864d557a926405908e1514b58bd33874c07ed6e9ae05c02b74f170958f7852 in /usr/local/share/ca-certificates/
        usr/
        usr/local/
        usr/local/share/
        usr/local/share/ca-certificates/
        usr/local/share/ca-certificates/NeweggintranetG2.crt

COPY file:2ad2756d24189c8d997032b02e5364d5f201a51956c40c7250c7c5a801d0e3b1 in /usr/local/share/ca-certificates/
        usr/
        usr/local/
        usr/local/share/
        usr/local/share/ca-certificates/
        usr/local/share/ca-certificates/neweggldev.crt

RUN chmod 644 /usr/local/share/ca-certificates/*  \
        && update-ca-certificates
USER [selenium]
RUN mkdir -p /home/selenium/.pki/nssdb  \
        && certutil -d /home/selenium/.pki/nssdb -N  \
        && certutil -d sql:/home/selenium/.pki/nssdb -L  \
        && certutil -d sql:/home/selenium/.pki/nssdb -A -t "CT,c,c" -n neweggrootg2 -i /usr/local/share/ca-certificates/NeweggRootG2.crt  \
        && certutil -d sql:/home/selenium/.pki/nssdb -A -t "CT,c,c" -n neweggintrg2 -i /usr/local/share/ca-certificates/NeweggintranetG2.crt  \
        && certutil -d sql:/home/selenium/.pki/nssdb -A -t "P,," -n neweggqa -i /usr/local/share/ca-certificates/neweggqa.crt  \
        && certutil -d sql:/home/selenium/.pki/nssdb -A -t "P,," -n neweggldev -i /usr/local/share/ca-certificates/neweggldev.crt
```

