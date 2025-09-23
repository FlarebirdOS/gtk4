pkgname=(
    gtk4
    gtk4-demos
    gtk4-docs
    gtk-update-icon-cache
)
pkgbase=gtk4
pkgver=4.20.1
pkgrel=2
pkgdesc="GObject-based multi-platform GUI toolkit"
arch=('x86_64')
url="https://www.gtk.org"
license=('LGPL-2.1-or-later')
depends=(
    'adwaita-fonts'
    'adwaita-icon-theme'
    'at-spi2-core'
    'cairo'
    'dconf'
    'desktop-file-utils'
    'fontconfig'
    'fribidi'
    'gcc-libs'
    'gdk-pixbuf'
    'glib2'
    'graphene'
    'gst-plugins-bad-libs'
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
source=(https://download.gnome.org/sources/gtk/${pkgver%.*}/${pkgbase%4}-${pkgver}.tar.xz
    gtk-update-icon-cache.{hook,script}
    gtk4-querymodules.{hook,script})
sha256sums=(bf32fac019171c45681b2c45d05658ee8598c6341572e32a6a6bcb0b5673998d
    2d435e3bec8b79b533f00f6d04decb1d7c299c6e89b5b175f20be0459f003fe8
    be873974b1cbefaa0f28a7478c87d1631400cc3e4bfbacd5378d5436ed8a7b6f
    d32462ab80a38ef42c75c1841ee4a2a5b1dfc5cbce98442bd15918f0d0c82be0
    5326a8bf7f132b23ea5293de60573a1382b9f5733bdbed5f23940e87bd7a8375)

prepare() {
    cd ${pkgbase%4}-${pkgver}

    sed -e '939 s/= { 0, }//'                                       \
        -e '940 a memset (&transform, 0, sizeof(GtkCssTransform));' \
        -i gtk/gtkcsstransformvalue.c
}

build() {
    cd ${pkgbase%4}-${pkgver}

    local meson_args=(
        -D broadway-backend=true
        -D vulkan=enabled
        -D documentation=true
        -D cloudproviders=enabled
        -D tracker=enabled
        -D colord=enabled
        -D man-pages=true
        -D sysprof=enabled
    )

    CFLAGS+=" -DG_DISABLE_CAST_CHECKS"

    ${meson_options} ${meson_args[@]}

    ${meson_build}
}

package_gtk4() {
    depends+=(
        'bash'
        'glibc'
        'gst-plugins-base-libs'
        'gstreamer'
        'gtk-update-icon-cache'
        'vulkan-loader'
    )

    cd ${pkgbase%4}-${pkgver}

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
    )

    mv guic/* ${pkgdir}

    ln -sr ${pkgdir}/usr/bin/gtk{4,}-update-icon-cache
    ln -sr ${pkgdir}/usr/share/man/man1/gtk{4,}-update-icon-cache.1

    install -Dt $pkgdir/usr/share/libalpm/hooks -m644 ${srcdir}/gtk-update-icon-cache.hook
    install -D ${srcdir}/gtk-update-icon-cache.script \
    $pkgdir/usr/share/libalpm/scripts/gtk-update-icon-cache
}
