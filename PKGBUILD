pkgname=(
    gtk4
    gtk4-demos
    gtk4-docs
    gtk-update-icon-cache
)
pkgbase=gtk4
pkgver=4.20.2
pkgrel=3
pkgdesc="GObject-based multi-platform GUI toolkit"
arch=('x86_64')
url="https://www.gtk.org"
license=('LGPL-2.1-or-later')
depends=(
    'adwaita-fonts'
    'adwaita-icon-theme'
    'at-spi2-core'
    'bash'
    'cairo'
    'dconf'
    'desktop-file-utils'
    'fontconfig'
    'fribidi'
    'gcc-libs'
    'gdk-pixbuf'
    'glib2'
    'glibc'
    'graphene'
    'gst-plugins-bad-libs'
    'gst-plugins-base-libs'
    'gstreamer'
    'gtk-update-icon-cache'
    'harfbuzz'
    'iso-codes'
    'libcloudproviders'
    'libcolord'
    'libcups'
    'libepoxy'
    'libglvnd'
    'libjpeg-turbo'
    'libpng'
    'librsvg'
    'libtiff'
    'libx11'
    'libxcursor'
    'libxdamage'
    'libxext'
    'libxfixes'
    'libxi'
    'libxinerama'
    'libxkbcommon'
    'libxrandr'
    'libxrender'
    'pango'
    'shared-mime-info'
    'tinysparql'
    'vulkan-loader'
    'wayland'
)
makedepends=(
    'docbook-xsl'
    'git'
    'glib2-devel'
    'gobject-introspection'
    'hicolor-icon-theme'
    'libsysprof-capture'
    'meson'
    'python-docutils'
    'python-gi-docgen'
    'python-pygobject3'
    'sassc'
    'shaderc'
    'vulkan-headers'
    'wayland-protocols'
)
source=(git+https://gitlab.gnome.org/GNOME/gtk.git#tag=${pkgver}
    gtk-update-icon-cache.{hook,script}
    gtk4-querymodules.{hook,script}
    0001-HACK-Don-t-use-objcopy-for-resource-embedding.patch)
sha256sums=(7730d0a95d3247fc436d5212294c771fccf47f9db3f8de0221a4fc83631c6eee
    2d435e3bec8b79b533f00f6d04decb1d7c299c6e89b5b175f20be0459f003fe8
    be873974b1cbefaa0f28a7478c87d1631400cc3e4bfbacd5378d5436ed8a7b6f
    d32462ab80a38ef42c75c1841ee4a2a5b1dfc5cbce98442bd15918f0d0c82be0
    5326a8bf7f132b23ea5293de60573a1382b9f5733bdbed5f23940e87bd7a8375
    46d6462c0e64479e96210c0762f206fb496d528fc9c6fa7f7aa7543ed90cfc2f)

prepare() {
    cd ${pkgbase%4}

    # Allow -fcf-protection to work
    # https://gitlab.gnome.org/GNOME/gtk/-/issues/6153
    git apply -3 ${srcdir}/0001-HACK-Don-t-use-objcopy-for-resource-embedding.patch
}

build() {
    cd ${pkgbase%4}

    local meson_args=(
        -D broadway-backend=true
        -D cloudproviders=enabled
        -D colord=enabled
        -D documentation=true
        -D man-pages=true
        -D sysprof=enabled
        -D tracker=enabled
    )

    CFLAGS+=" -DG_DISABLE_CAST_CHECKS"

    ${meson_options} ${meson_args[@]}

    ${meson_build}
}

package_gtk4() {
    cd ${pkgbase%4}

    ${meson_install} ${pkgdir}

    install -Dm644 /dev/stdin ${pkgdir}/usr/share/gtk-4.0/settings.ini <<END
[Settings]
gtk-icon-theme-name = Adwaita
gtk-theme-name = Adwaita
gtk-font-name = Cantarell 11
END

    install -Dt ${pkgdir}/usr/share/libalpm/hooks -m644 ${srcdir}/gtk4-querymodules.hook
    install -D ${srcdir}/gtk4-querymodules.script ${pkgdir}/usr/share/libalpm/scripts/gtk4-querymodules

    _pick demo ${pkgdir}/usr/bin/gtk4-{demo,demo-application,node-editor,print-editor,widget-factory}
    _pick demo ${pkgdir}/usr/share/applications/org.gtk.{Demo4,PrintEditor4,WidgetFactory4,gtk4.NodeEditor}.desktop
    _pick demo ${pkgdir}/usr/share/glib-2.0/schemas/org.gtk.Demo4.gschema.xml
    _pick demo ${pkgdir}/usr/share/icons/hicolor/*/apps/org.gtk.{Demo4,PrintEditor4,WidgetFactory4,gtk4.NodeEditor}[-.]*
    _pick demo ${pkgdir}/usr/share/man/man1/gtk4-{demo,demo-application,node-editor,widget-factory}.1
    _pick demo ${pkgdir}/usr/share/metainfo/org.gtk.{Demo4,PrintEditor4,WidgetFactory4,gtk4.NodeEditor}.appdata.xml

    _pick docs ${pkgdir}/usr/share/doc

    _pick guic ${pkgdir}/usr/bin/gtk4-update-icon-cache
    _pick guic ${pkgdir}/usr/share/man/man1/gtk4-update-icon-cache.1
}

package_gtk4-demos() {
    pkgdesc+=" (demo applications)"
    depends=(
        'cairo'
        'dconf'
        'gdk-pixbuf'
        'glib2'
        'glibc'
        'graphene'
        'gtk4'
        'harfbuzz'
        'hicolor-icon-theme'
        'libepoxy'
        'librsvg'
        'pango'
    )

    mv demo/* ${pkgdir}
}

package_gtk4-docs() {
    pkgdesc+=" (documentation)"
    depends=()

    mv docs/* ${pkgdir}
}

package_gtk-update-icon-cache() {
    pkgdesc="GTK icon cache updater"
    depends=(
        'bash'
        'gdk-pixbuf'
        'glib2'
        'glibc'
        'hicolor-icon-theme'
        'librsvg'
    )

    mv guic/* ${pkgdir}

    ln -sr ${pkgdir}/usr/bin/gtk{4,}-update-icon-cache
    ln -sr ${pkgdir}/usr/share/man/man1/gtk{4,}-update-icon-cache.1

    install -Dt $pkgdir/usr/share/libalpm/hooks -m644 ${srcdir}/gtk-update-icon-cache.hook
    install -D ${srcdir}/gtk-update-icon-cache.script \
    $pkgdir/usr/share/libalpm/scripts/gtk-update-icon-cache
}
