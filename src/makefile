SHELL=/bin/bash
CFLAGS=--std=c99

prefix=/usr
bindir=$(prefix)/bin
sharedir=$(prefix)/share
localedir=$(sharedir)/locale
launcherdir=$(sharedir)/applications
polkitdir=$(sharedir)/polkit-1/actions
mandir=$(sharedir)/man
man1dir=$(mandir)/man1

app_name=ukuu

vte_version=$(shell { ((ldconfig -p | grep libvte-2.91*.so) >/dev/null && echo 'vte-2.91') || echo 'vte-2.90'; } )
define_symbols=$(shell { ((ldconfig -p | grep libvte-2.91*.so) >/dev/null && echo '-D VTE_291') || echo ''; } )

all:
	#build binaries
	
	#app-gtk
	valac -X -D'GETTEXT_PACKAGE="${app_name}"' --Xcc="-lm" -X -Wl,-rpath,/usr/share/${app_name}/libs ${define_symbols} --thread Common/*.vala Gtk/*.vala Utility/*.vala Utility/Gtk/*.vala -o ${app_name}-gtk --pkg glib-2.0 --pkg gio-unix-2.0 --pkg posix --pkg gtk+-3.0 --pkg gee-0.8 --pkg json-glib-1.0 --pkg $(vte_version)

	#app
	valac -X -D'GETTEXT_PACKAGE="${app_name}"' --Xcc="-lm" -X -Wl,-rpath,/usr/share/${app_name}/libs --thread Common/*.vala Console/*.vala Utility/*.vala Utility/Gtk/*.vala Gtk/UpdateNotificationWindow.vala -o ${app_name} --pkg glib-2.0 --pkg gio-unix-2.0 --pkg posix --pkg gtk+-3.0 --pkg gee-0.8 --pkg libsoup-2.4 --pkg json-glib-1.0 --pkg $(vte_version)

	#update translation template
	find . -iname "*.vala" | xargs xgettext --from-code=UTF-8 --language=C --keyword=_ --copyright-holder='Tony George (teejeetech@gmail.com)' --package-name='${app_name}' --package-version='2.2' --msgid-bugs-address='teejeetech@gmail.com' --escape --sort-output -o ../${app_name}.pot

clean:
	rm -rfv ../release/{source,i386,amd64,armel,armhf}
	rm -rfv ../release/*.{run,deb}
	rm -rfv *.c *.o *.mo
	rm -fv ${app_name} ${app_name}-gtk

install:
	mkdir -p "$(DESTDIR)$(bindir)"
	mkdir -p "$(DESTDIR)$(sharedir)"
	mkdir -p "$(DESTDIR)$(mandir)"
	mkdir -p "$(DESTDIR)$(man1dir)"
	mkdir -p "$(DESTDIR)$(launcherdir)"
	mkdir -p "$(DESTDIR)$(polkitdir)"
	mkdir -p "$(DESTDIR)$(sharedir)/glib-2.0/schemas/"
	mkdir -p "$(DESTDIR)$(sharedir)/${app_name}"
	mkdir -p "$(DESTDIR)$(sharedir)/pixmaps"

	#binary
	install -m 0755 ${app_name} "$(DESTDIR)$(bindir)"
	install -m 0755 ${app_name}-gtk "$(DESTDIR)$(bindir)"
	install -m 0755 ${app_name}-uninstall "$(DESTDIR)$(bindir)"

	#shared files
	cp -dpr --no-preserve=ownership -t "$(DESTDIR)$(sharedir)/${app_name}" ./share/${app_name}/*
	chmod --recursive 0755 $(DESTDIR)$(sharedir)/${app_name}/*

	#polkit policy file
	#install -m 0644 ./share/polkit-1/actions/in.teejeetech.pkexec.ukuu-gtk.policy "$(DESTDIR)$(polkitdir)"
	#rm -f "$(DESTDIR)$(polkitdir)/in.teejeetech.pkexec.ukuu-gtk.policy" 

	#launcher
	install -m 0755 ${app_name}.desktop "$(DESTDIR)$(launcherdir)"
	
	#app icon
	install -m 0755 ./share/pixmaps/${app_name}.* "$(DESTDIR)$(sharedir)/pixmaps"

	#translations
	mkdir -p "$(DESTDIR)$(localedir)/sv/LC_MESSAGES"
	msgfmt --check --verbose -o "$(DESTDIR)$(localedir)/sv/LC_MESSAGES/${app_name}.mo" ../po/${app_name}-sv.po
	mkdir -p "$(DESTDIR)$(localedir)/ru/LC_MESSAGES"
	msgfmt --check --verbose -o "$(DESTDIR)$(localedir)/ru/LC_MESSAGES/${app_name}.mo" ../po/${app_name}-ru.po
	
uninstall:
	#binary
	rm -f "$(DESTDIR)$(bindir)/${app_name}"
	rm -f "$(DESTDIR)$(bindir)/${app_name}-uninstall"

	#shared files
	rm -rf "$(DESTDIR)$(sharedir)/${app_name}"

	#polkit policy file
	#rm -f "$(DESTDIR)$(polkitdir)/in.teejeetech.pkexec.ukuu-gtk.policy"

	#launcher
	rm -f "$(DESTDIR)$(launcherdir)/${app_name}.desktop"
	
	#app icon
	rm -f "$(DESTDIR)$(sharedir)/pixmaps/${app_name}.png"

    #translations
	rm -f $(DESTDIR)$(localedir)/*/LC_MESSAGES/${app_name}.mo

	#startup scripts
	rm -f $(DESTDIR)/home/*/.config/ukuu-notify.sh
	rm -f $(DESTDIR)/home/*/.config/autostart/ukuu.desktop
