#
# T-Coffee legacy make file -- used mainly by the distrution and packaging procedure 
#
PROGRAM=T-COFFEE
EXECUTABLE=t_coffee
AUTHOR=Cedric Notredame 
DATE=$(shell date '+%d %b %Y - %H:%M')
DISTRIBUTION_ADDRESS=www.tcoffee.org/Packages
INSTITUTE=Centro de Regulacio Genomica
EMAIL=cedric.notredame@gmail.com
SHELL := /bin/bash

#
# make it possibile to define HOME2 externally using environment
# variable named 'SANDBOX', otherwise fallback on the defult mechanism
#
ifndef HOME2
HOME2=$(HOME)/Dropbox
endif


#########################################################
#                                                       #
#       COMPILERS AND FLAGS                             #
#                                                       #
#                                                       #
#                                                       #
#########################################################

CC=g++ 
ifndef CFLAGS
#CFLAGS=-O3 -Wno-write-strings
CFLAGS=-ggdb -w
endif

ifdef OPENMP
    KM_FLAGS=-fopenmp
endif

ifndef LIB_DIR
LIB_DIR=../../lib
endif

SOURCE_DIR=$(PWD)/../../$(EXECUTABLE)/lib
SCRIPT_DIR=$(PWD)/../../$(EXECUTABLE)/scripts


#
# Base path for library folders
#

ifdef NORM
NORM_2_USE=util_dp_gotoh_nw_2.c
endif

ifndef NORM
NORM_2_USE=util_dp_gotoh_nw.c
endif


ifndef USER_BIN
USER_BIN=$(HOME)/bin/
endif

CODE_LIST=$(LIB_DIR)/compilation/code_list
PERL_LIB_DIR=$(LIB_DIR)/perl/lib
KO_LIB=$(LIB_DIR)/data_headers

#LIST OF PERL MODULES
SCRIPT_LIB=$(PERL_LIB_DIR)/scripts
PERL4MAKEFILE_LIB=$(PERL_LIB_DIR)/perl4makefile

#LIST OF LICENSES
LICENSES = $(LIB_DIR)/licenses

#
# make version number condition so that it can specified
# using env variable otherwise it fallback on legacy mechanism
#
ifndef VERSION
VERSION=$(shell $(PERL4MAKEFILE_LIB)/read_program_version.pl $(LIB_DIR)/version_number.version)
endif


LIBRARY=$(PWD)/../../lib/


DISTRIBUTION_FILE=$(PROGRAM)_distribution_$(VERSION)
LATEST_DISTRIBUTION_FILE=$(PROGRAM)_distribution
DISTRIBUTIONS=$(HOME2)/distributions/
UNTARED_DISTRIBUTIONS=$(HOME2)/untared_distributions/
LATEST_DISTRIBUTIONS=$(HOME2)/latest_distributions/
DISTRIB_DIR=$(HOME2)/distributions/$(PROGRAM)_distribution_$(VERSION)
DISTRIB_DOC=$(DISTRIB_DIR)/doc


#########################################################
#                                                       #
#       DISTRIBUTION                                    #
#                                                       #
#                                                       #
#                                                       #
#########################################################

distribution:
	-rm *.c
	-rm *.h
	make -i VERSION=$(VERSION) CC=cc general_distribution
	make -i VERSION=$(VERSION) CC=cc examples
	make -i VERSION=$(VERSION) CC=cc mcoffee
	make -i VERSION=$(VERSION) CC=cc packing

doc:
	cp $(PWD)/../../$(EXECUTABLE)/doc/$(EXECUTABLE)_tutorial.* $(DISTRIB_DOC)
	antiword -m  "UTF-8" $(PWD)/../../$(EXECUTABLE)/doc/$(EXECUTABLE)_tutorial.doc >$(DISTRIB_DOC)/$(EXECUTABLE)_tutorial.txt
	cp $(PWD)/../../$(EXECUTABLE)/doc/$(EXECUTABLE)_technical.* $(DISTRIB_DOC)
	antiword -m "UTF-8" $(PWD)/../../$(EXECUTABLE)/doc/$(EXECUTABLE)_technical.doc >$(DISTRIB_DOC)/$(EXECUTABLE)_technical.txt
	cp $(PWD)/../../$(EXECUTABLE)/doc/README $(DISTRIB_DIR)
	cp $(PWD)/../../$(EXECUTABLE)/doc/README $(DISTRIB_DIR)/doc/
	cp $(PWD)/../../$(EXECUTABLE)/doc/aln_compare.doc.txt $(DISTRIB_DOC)
	cp $(PWD)/../../$(EXECUTABLE)/doc/seq_reformat.doc.txt $(DISTRIB_DOC)
	cp $(PWD)/../../$(EXECUTABLE)/doc/$(EXECUTABLE).pdf $(DISTRIB_DOC)

mcoffee:
	-rm -rf $(DISTRIB_DIR)/mcoffee
	mkdir -p $(DISTRIB_DIR)/mcoffee
	cp $(LIBRARY)/mcoffee/* $(DISTRIB_DIR)/mcoffee

examples:
	mkdir -p $(DISTRIB_DIR)/example/
	cp $(PWD)/../../$(EXECUTABLE)/doc_test/data/*  $(DISTRIB_DIR)/example/

test:
	mkdir -p $(DISTRIB_DIR)/test
	cp $(PWD)/../test/test.pep $(DISTRIB_DIR)/test/test.pep
	$(EXECUTABLE)  $(DISTRIB_DIR)/test/test.pep -in=Mfast_pair -outfile=$(DISTRIB_DIR)/test/reference_test.aln -outorder=input
	echo '#!/usr/bin/env perl'    > $(DISTRIB_DIR)/bin/test.pl
	echo 'chdir "./test";' >> $(DISTRIB_DIR)/bin/test.pl
	echo '$$r=system "../bin/t_coffee test.pep -method fast_pair -outfile=new_reference_test.aln -outorder=input>/dev/null";' >> $(DISTRIB_DIR)/bin/test.pl
	echo 'if ($$r==0){print "\\nInstallation of $(EXECUTABLE) Successful\\n";}'>>$(DISTRIB_DIR)/bin/test.pl
	echo 'else {print "\\nInstallation of $(EXECUTABLE) Not Successful\\n";}'>>$(DISTRIB_DIR)/bin/test.pl
	echo 'chdir "/..";'
	chmod u+x $(DISTRIB_DIR)/bin/test.pl
	#echo "./bin/test.pl" >>$(DISTRIB_DIR)/install
	#echo "./bin/t_coffee -update" >>$(DISTRIB_DIR)/install

debug: t_coffee
	rm -r debug
	mkdir debug
	cp *.c debug
	cp *.h debug
	mv debug $(HOME2)/debug

env:
	@echo 'Home             :' $(HOME)
	@echo 'Pwd              :' $(PWD)
	@echo 'VERSION          :' $(VERSION)
	@echo 'DATE             :' $(DATE)
	@echo 'OSNAME           :' $(OSNAME)
	@echo 'OSARCH           :' $(OSARCH)
	@echo 'HOME2            :' $(HOME2)
	@echo 'CFLAGS           :' $(CFLAGS)
	@echo 'KM_FLAGS         :' $(KM_FLAGS)
	@echo 'DISTRIB_DIR      :' $(DISTRIB_DIR)
	@echo 'DISTRIB_DOC      :' $(DISTRIB_DOC)
	@echo 'EXECUTABLE       :' $(EXECUTABLE)
	@echo 'USER_BIN         :' $(USER_BIN)
	@echo 'LIB_DIR          :' $(LIB_DIR)
	@echo 'LIBRARY          :' $(LIBRARY)
	@echo 'DISTRIBUTIONS    :' $(DISTRIBUTIONS)


general_distribution:
	-rm $(CODE_LIST);
	mkdir -p $(DISTRIB_DIR)
	mkdir -p $(DISTRIB_DIR)/bin
	mkdir -p $(DISTRIB_DOC)
	mkdir -p $(DISTRIB_DIR)/$(EXECUTABLE)_source
	mkdir -p $(DISTRIB_DIR)/reference_test
	echo '' > $(DISTRIB_DIR)/install
	make -i VERSION=$(VERSION) CC=cc code
	make -i VERSION=$(VERSION) CC=cc license

library_distribution:
	cd $(LIBRARY)/..
	tar -cvf lib.tar $(LIBRARY)
	gzip lib.tar
	mkdir $(DISTRIBUTIONS)
	mv lib.tar.gz $(DISTRIBUTIONS)/lib_$(VERSION).tar.gz

empty:
	-rm *.o_$(OSNAME)
	tar -cf old_c.tar *.c *.h
	rm *.c
	rm *.h
	echo x > core
	rm core

distribute: all
	echo x > core
	rm core
	-rm *.o_$(OSNAME)
code:
	echo $(OSNAME) Compilation
	make clean
	make CC="g++" $(EXECUTABLE)
	mkdir -p $(DISTRIB_DIR)/$(EXECUTABLE)_source
	make copy_sources
	$(PERL4MAKEFILE_LIB)/tclinkdb2header.pl -infile=$(SCRIPT_LIB)/install.pl -mode=Perl -db=$(KO_LIB)/tclinkdb.txt >$(DISTRIB_DIR)/install
	cp $(KO_LIB)/tclinkdb.txt $(DISTRIB_DIR)
	chmod u+x $(DISTRIB_DIR)/install

# 
# This target copies T-Coffee sources files from the library folder 
# to the distribution folder. 
# This process is a bit tricky to maintaning compatibiltiy with the legacy 
# build process. It uses two temporary files 'code_list' and 'foldes'.
# 
# The file "code_list" is generated during the code compilation and contains
# the list of all C sources requied to compile T-Coffee. The target below 
# copies all the contained files to the target distribution directory. 
# 
# During this copy it is created a file "folders" with all the 
# sub-directories containing the used C sources. 
# 
# Only the header files "*.h" in that directories will be copied to the distrubtion folder�
# 
copy_sources: 
	-rm -rf folders
	for it in `cat code_list`; do \
	  dir=$(LIB_DIR); \
	  entry=$${it/$$dir/}; \
	  path=`dirname $$entry`; \
	  file=`basename $$entry`; \
	  mkdir -p $(DISTRIB_DIR)/$(EXECUTABLE)_source/$$path; \
	  cp $$it $(DISTRIB_DIR)/$(EXECUTABLE)_source/$$path; \
	  echo "$$path" >> folders; \
	done

# Always add the current and 'data_headers' folders
	echo "." >> folders
	echo "data_headers" >> folders
# Add the 'header' files
	for path in `cat folders | uniq`; do \
	  mkdir -p $(DISTRIB_DIR)/$(EXECUTABLE)_source/$$path; \
	  cp $(LIB_DIR)/$$path/*.h $(DISTRIB_DIR)/$(EXECUTABLE)_source/$$path; \
	done
# Copy these headers that are generated/upodated automatically by the build process
	cp ./perl_header_lib.h $(DISTRIB_DIR)/$(EXECUTABLE)_source
	cp ./programmes_define.h $(DISTRIB_DIR)/$(EXECUTABLE)_source
	cp ./define_header.h $(DISTRIB_DIR)/$(EXECUTABLE)_source

# Some missing stuff  	
	cp $(LIB_DIR)/fortran/TMalign.f $(DISTRIB_DIR)/$(EXECUTABLE)_source
	cp $(LIB_DIR)/c_make_lib/dist_makefile $(DISTRIB_DIR)/$(EXECUTABLE)_source/makefile


license:
	$(PERL4MAKEFILE_LIB)/make_license.pl -file $(LICENSES)/academic_license.txt -email "$(EMAIL)" -type txt -institute "$(INSTITUTE)" -author "$(AUTHOR)" -date "$(DATE)" -program "$(PROGRAM)" > $(DISTRIB_DIR)/license.txt
	$(PERL4MAKEFILE_LIB)/make_license.pl -file $(LICENSES)/short_license_utf8.txt -email "$(EMAIL)" -type c -institute "$(INSTITUTE)" -author "$(AUTHOR)" -date "$(DATE)" -program "$(PROGRAM)" > $(DISTRIB_DIR)/short_license.c
#$(PERL4MAKEFILE_LIB)/make_license.pl -file $(LICENSES)/short_license.txt -email "$(EMAIL)" -type perl -institute "$(INSTITUTE)" -author "$(AUTHOR)" -date "$(DATE)" -program "$(PROGRAM)" > $(DISTRIB_DIR)/short_license.pl
	for file in `find $(DISTRIB_DIR)/$(EXECUTABLE)_source \( -name '*.h' -o -name '*.c' \)` ; do \
	  cat $(DISTRIB_DIR)/short_license.c $$file > $$file.new; \
	  mv $$file.new $$file; \
	done
	rm $(DISTRIB_DIR)/short_license.c

#install:
#   $(PERL4MAKEFILE_LIB)/tclinkdb2header.pl -infile=$(SCRIPT_LIB)/install.pl -mode=Perl -db=$(KO_LIB)/tclinkdb.txt > install

packing:
	mkdir -p $(UNTARED_DISTRIBUTIONS)
	mkdir -p $(DISTRIBUTIONS)
	mkdir -p $(LATEST_DISTRIBUTIONS)
	mv $(DISTRIB_DIR) .
	tar -cvf $(DISTRIBUTION_FILE).tar $(DISTRIBUTION_FILE)
	gzip $(DISTRIBUTION_FILE).tar
	mv $(DISTRIBUTION_FILE)        $(UNTARED_DISTRIBUTIONS)
	cp $(DISTRIBUTION_FILE).tar.gz $(DISTRIBUTIONS)
	cp $(DISTRIBUTION_FILE).tar.gz $(LATEST_DISTRIBUTIONS)/$(LATEST_DISTRIBUTION_FILE).tar.gz
	rm $(DISTRIBUTION_FILE).tar.gz


#
# Include the main T-Coffee makefile
#
include ../../compile/makefile 
