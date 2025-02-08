# appimage-builder recipe see https://appimage-builder.readthedocs.io for details
version: 1
script:
  - rm -rf ./AppDir || true
  - bsdtar -zxvf blocklink.deb
  - tar -xvf ./data.tar.xz
  - mkdir ./AppDir
  - mv ./usr ./AppDir/usr
  # 32x32 icon
  - for i in {32,64,128}; do mkdir -p ./AppDir/usr/share/icons/hicolor/${i}x${i}/apps/; cp ../res/${i}x${i}.png ./AppDir/usr/share/icons/hicolor/${i}x${i}/apps/blocklink.png; done
  - mkdir -p ./AppDir/usr/share/icons/hicolor/scalable/apps/; cp ../res/scalable.svg ./AppDir/usr/share/icons/hicolor/scalable/apps/blocklink.svg
  # desktop file
  # - sed -i "s|Icon=/usr/share/blocklink/files/blocklink.png|Icon=blocklink|g" ./AppDir/usr/share/applications/blocklink.desktop
  - rm -rf ./AppDir/usr/share/applications

AppDir:
  path: ./AppDir
  app_info:
    id: blocklink
    name: blocklink
    icon: blocklink
    version: 1.3.8
    exec: usr/share/blocklink/blocklink
    exec_args: "$@"
  apt:
    arch:
      - amd64
    allow_unauthenticated: true
    sources:
      - sourceline: deb http://archive.ubuntu.com/ubuntu/ focal main restricted
      - sourceline: deb http://archive.ubuntu.com/ubuntu/ focal-updates main restricted
      - sourceline: deb http://archive.ubuntu.com/ubuntu/ focal universe
      - sourceline: deb http://archive.ubuntu.com/ubuntu/ focal-updates universe
      - sourceline: deb http://archive.ubuntu.com/ubuntu/ focal multiverse
      - sourceline: deb http://archive.ubuntu.com/ubuntu/ focal-updates multiverse
      - sourceline: deb http://archive.ubuntu.com/ubuntu/ focal-backports main restricted universe multiverse
      - sourceline: deb http://archive.ubuntu.com/ubuntu/ focal-security main restricted universe multiverse
    include:
      - libc6:amd64
      - libgtk-3-0
      - libxcb-randr0
      - libxdo3
      - libxfixes3
      - libxcb-shape0
      - libxcb-xfixes0
      - libasound2
      - libsystemd0
      - curl
      - libva2
      - libva-drm2
      - libva-x11-2
      - libgstreamer-plugins-base1.0-0
      - gstreamer1.0-pipewire
      - libwayland-client0
      - libwayland-cursor0
      - libwayland-egl1
      - libpulse0
      - packagekit-gtk3-module
      - libcanberra-gtk3-module
      - libpam0g
      - libdrm2
    exclude:
      - humanity-icon-theme
      - hicolor-icon-theme
      - adwaita-icon-theme
      - ubuntu-mono
  files:
    include: []
    exclude:
      - usr/share/man
      - usr/share/doc/*/README.*
      - usr/share/doc/*/changelog.*
      - usr/share/doc/*/NEWS.*
      - usr/share/doc/*/TODO.*
  runtime:
    env:
      GIO_MODULE_DIR: /lib64/gio/modules:/usr/lib/x86_64-linux-gnu/gio/modules:$APPDIR/usr/lib/x86_64-linux-gnu/gio/modules
      GDK_BACKEND: x11
      APPDIR_LIBRARY_PATH: /lib64:/usr/lib/x86_64-linux-gnu:$APPDIR/lib/x86_64-linux-gnu:$APPDIR/lib/x86_64-linux-gnu/security:$APPDIR/lib/systemd:$APPDIR/usr/lib/x86_64-linux-gnu:$APPDIR/usr/lib/x86_64-linux-gnu/gdk-pixbuf-2.0/2.10.0/loaders:$APPDIR/usr/lib/x86_64-linux-gnu/gstreamer-1.0:$APPDIR/usr/lib/x86_64-linux-gnu/gtk-3.0/3.0.0/immodules:$APPDIR/usr/lib/x86_64-linux-gnu/gtk-3.0/3.0.0/printbackends:$APPDIR/usr/lib/x86_64-linux-gnu/krb5/plugins/preauth:$APPDIR/usr/lib/x86_64-linux-gnu/libcanberra-0.30:$APPDIR/usr/lib/x86_64-linux-gnu/pulseaudio:$APPDIR/usr/lib/x86_64-linux-gnu/sasl2:$APPDIR/usr/lib/x86_64-linux-gnu/vdpau:$APPDIR/usr/share/blocklink/lib:$APPDIR/lib/x86_64
      GST_PLUGIN_PATH: /lib64/gstreamer-1.0:/usr/lib/x86_64-linux-gnu/gstreamer-1.0:$APPDIR/usr/lib/x86_64-linux-gnu/gstreamer-1.0
      GST_PLUGIN_SYSTEM_PATH: /lib64/gstreamer-1.0:/usr/lib/x86_64-linux-gnu/gstreamer-1.0:$APPDIR/usr/lib/x86_64-linux-gnu/gstreamer-1.0

test:
  fedora-30:
    image: appimagecrafters/tests-env:fedora-30
    command: ./AppRun
  debian-stable:
    image: appimagecrafters/tests-env:debian-stable
    command: ./AppRun
  archlinux-latest:
    image: appimagecrafters/tests-env:archlinux-latest
    command: ./AppRun
  centos-7:
    image: appimagecrafters/tests-env:centos-7
    command: ./AppRun
  ubuntu-xenial:
    image: appimagecrafters/tests-env:ubuntu-xenial
    command: ./AppRun

AppImage:
  arch: x86_64
  update-information: guess
