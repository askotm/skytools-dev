
include ../config.mak

wiki = https://developer.skype.com/SkypeGarage/DbProjects/SkyTools

web = mkz@shell.pgfoundry.org:/home/pgfoundry.org/groups/skytools/htdocs/skytools-3.0

EPYDOC = epydoc
EPYARGS = --no-private --url="http://pgfoundry.org/projects/skytools/" \
	--name="Skytools" --html --no-private -v

HTMLS = londiste.cmdline.html londiste.config.html README.html INSTALL.html \
	londiste.ref.html TODO.html pgq-sql.html pgq-admin.html pgq-nodupes.html \
	$(SCRIPT_HTMLS) faq.html set.notes.html qadmin.html skytools3.html devnotes.html

SCRIPT_TXTS = walmgr.txt cube_dispatcher.txt table_dispatcher.txt \
	      queue_mover.txt queue_splitter.txt bulk_loader.txt \
	      scriptmgr.txt skytools_upgrade.txt
SCRIPT_MANS = $(SCRIPT_TXTS:.txt=.1)
SCRIPT_HTMLS = $(SCRIPT_TXTS:.txt=.html)

COMMON = common.switches.txt common.config.txt common.logutriga.txt

GETATTRS = python ./getattrs.py

#AFLAGS = -a linkcss
#AFLAGS = -a stylesheet=extra.css

all: man $(HTMLS)

man: londiste.1 londiste.5 pgqadm.1 $(SCRIPT_MANS)

html: $(HTMLS)

install: man $(HTMLS)
	mkdir -p $(DESTDIR)/$(mandir)/man1
	mkdir -p $(DESTDIR)/$(mandir)/man5
	mkdir -p $(DESTDIR)/$(docdir)
	install -m 644 londiste.1 $(DESTDIR)/$(mandir)/man1
	install -m 644 londiste.5 $(DESTDIR)/$(mandir)/man5
	install -m 644 pgqadm.1 $(DESTDIR)/$(mandir)/man1
	for m in $(SCRIPT_MANS); do \
	  install -m 644 $$m $(DESTDIR)/$(mandir)/man1 ; \
	done
	for h in $(HTMLS); do \
	  install -m 644 $$h $(DESTDIR)/$(docdir) ; \
	done

old.wiki.upload:
	devupload.sh overview.txt $(wiki)
	#devupload.sh TODO.txt $(wiki)/ToDo
	#devupload.sh londiste.txt $(wiki)/LondisteUsage
	#devupload.sh londiste.ref.txt $(wiki)/LondisteReference
	#devupload.sh pgq-sql.txt $(wiki)/PgQdocs
	#devupload.sh pgq-nodupes.txt $(wiki)/PgqNoDupes
	#devupload.sh walmgr.txt $(wiki)/WalMgr
	#devupload.sh pgq-admin.txt $(wiki)/PgqAdm

PY_PKGS = skytools pgq londiste

#	skytools.config skytools.dbstruct skytools.gzlog \
#	skytools.quoting skytools.scripting skytools.sqltools \
#	pgq pgq.consumer pgq.event pgq.maint pgq.producer pgq.status pgq.ticker \
#	londiste londiste.compare londiste.file_read londiste.file_write \
#	londiste.installer londiste.playback londiste.repair londiste.setup \
#	londiste.syncer londiste.table_copy

apidoc:
	rm -rf api
	mkdir -p api
	cd ../python && $(EPYDOC) $(EPYARGS) -o ../doc/api $(PY_PKGS)

apiupload: apidoc
	rsync -rtlz api/* $(web)/api
	cd ../sql/pgq && rm -rf docs/html && $(MAKE) dox
	rsync -rtlz ../sql/pgq/docs/html/* $(web)/pgq/
	cd ../sql/pgq_coop && rm -rf docs/html && $(MAKE) dox
	rsync -rtlz ../sql/pgq_coop/docs/html/* $(web)/pgq_coop/
	cd ../sql/pgq_node && rm -rf docs/html && $(MAKE) dox
	rsync -rtlz ../sql/pgq_node/docs/html/* $(web)/pgq_node/
	cd ../sql/londiste && rm -rf docs/html && $(MAKE) dox
	rsync -rtlz ../sql/londiste/docs/html/* $(web)/londiste/

clean:
	rm -rf api *.xml

distclean: clean
	rm -rf ../sql/pgq/docs/pgq

realclean: distclean
	rm -f *.[15] *.xml *.html

ifneq ($(ASCIIDOC),no)
ifneq ($(XMLTO),no)
londiste.1: londiste.cmdline.xml
	$(XMLTO) man $<

londiste.5: londiste.config.xml
	$(XMLTO) man $<

pgqadm.1: pgq-admin.xml
	$(XMLTO) man $<

walmgr.1: walmgr.xml
	$(XMLTO) man $<

%.xml: %.txt $(COMMON)
	$(ASCIIDOC) -b docbook -d manpage `$(GETATTRS) $<` -o - $< \
	| python fixman.py > $@

%.1: %.xml
	$(XMLTO) man $<
endif

%.html: %.txt $(COMMON)
	LANG=C cat $< \
	| sed -e '/^include/b;s,\([A-Za-z.0-9]*\)[.]txt,link:\1.html[],g' \
	| $(ASCIIDOC) $(AFLAGS) -a toc `$(GETATTRS) $<` -o - - \
	| sed -e "/^[/][*] Workarounds/r extra.css" \
	> $@

README.html: ../README
	cat $< \
	| sed -e 's,doc/\([!-~]*\)[.]txt,link:\1.html[],g' \
	      -e 's,http:[!-~]*,&[],g' \
	| $(ASCIIDOC) $(AFLAGS) -o - - \
	| sed -e "/^[/][*] Workarounds/r extra.css" \
	> $@

INSTALL.html: ../INSTALL
	$(ASCIIDOC) $(AFLAGS) -o - $< \
	| sed -e "/^[/][*] Workarounds/r extra.css" \
	> $@
endif

web: $(HTMLS)
	rsync -avz $(HTMLS) $(web)/doc/



