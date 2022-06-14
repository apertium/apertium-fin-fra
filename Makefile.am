###############################################################################
## Makefile for Finnish--French
###############################################################################

LANG1=fin
LANG2=fra
PREFIX1=$(LANG1)-$(LANG2)
PREFIX2=$(LANG2)-$(LANG1)

BASENAME=apertium-$(PREFIX1)

TARGETS_COMMON =		\
	$(PREFIX1).automorf.hfst	\
	$(PREFIX1).rlx.bin	\
	$(PREFIX1).autobil.bin	\
	$(PREFIX1).autolex.bin	\
	$(PREFIX1).autogen.bin	\
	$(PREFIX1).autopgen.bin	\
	$(PREFIX1).rtx.bin	\
	$(PREFIX2).automorf.bin $(PREFIX2).automorf-untrimmed.bin	\
	$(PREFIX2).rlx.bin	\
	$(PREFIX2).autobil.bin	\
	$(PREFIX2).autolex.bin	\
	$(PREFIX2).autogen.hfst	\
	$(PREFIX2).rtx.bin

# This include defines goals for install-modes, .deps/.d, autobil.prefixes and .mode files:
@ap_include@

###############################################################################
## Monolingual transducers
###############################################################################



# -------------------
# Finnish analysis:
# -------------------

.deps/$(LANG1).automorf.hfst: $(AP_SRC1)/$(LANG1).automorf.hfst .deps/.d
	cp -v $< $@

$(PREFIX1).automorf.hfst: .deps/$(LANG1).automorf.hfst
	cp -v $< $@

# -------------------
# Finnish generation:
# ---------------------



$(PREFIX2).autogen.hfst: $(AP_SRC1)/$(LANG1).autogen.hfst
	cp -v $< $@

# -------------------
# French analysis:
# -------------------

.deps/$(LANG2).automorf.bin: $(AP_SRC2)/$(LANG2).automorf.bin .deps/.d
	cp $< $@

$(PREFIX2).automorf-untrimmed.bin: .deps/$(LANG2).automorf.bin
	cp -v $< $@

$(PREFIX2).automorf.bin: .deps/$(LANG2).automorf.bin $(PREFIX2).autobil.bin
	lt-trim $^ $@

# -------------------
# French generation:
# ---------------------



$(PREFIX1).autogen.bin: $(AP_SRC2)/$(LANG2).autogen.bin
	cp $< $@

###############################################################################
## Tagger models
###############################################################################

$(PREFIX1).prob: $(AP_SRC1)/$(LANG1).prob
	cp $< $@

$(PREFIX2).prob: $(AP_SRC2)/$(LANG2).prob
	cp $< $@

###############################################################################
## Disambiguation rules
###############################################################################


$(PREFIX1).rlx.bin: $(AP_SRC1)/apertium-$(LANG1).$(LANG1).rlx
	cg-comp $< $@


$(PREFIX2).rlx.bin: $(AP_SRC2)/apertium-$(LANG2).$(LANG2).rlx
	cg-comp $< $@

###############################################################################
## Bilingual dictionaries
###############################################################################

$(PREFIX1).autobil.bin: $(BASENAME).$(PREFIX1).dix
	apertium-validate-dictionary $<
	lt-comp lr $< $@

$(PREFIX2).autobil.bin: $(BASENAME).$(PREFIX1).dix
	apertium-validate-dictionary $<
	lt-comp rl $< $@

###############################################################################
## Lexical selection rules
###############################################################################

$(PREFIX1).autolex.bin: $(BASENAME).$(PREFIX1).lrx
	lrx-comp $< $@

$(PREFIX2).autolex.bin: $(BASENAME).$(PREFIX2).lrx
	lrx-comp $< $@


###############################################################################
## Structural transfer rules
###############################################################################


$(PREFIX1).rtx.bin: $(BASENAME).$(PREFIX1).rtx
	rtx-comp $< $@

$(PREFIX2).rtx.bin: $(BASENAME).$(PREFIX2).rtx
	rtx-comp $< $@

###############################################################################
## Postgeneration dictionaries
###############################################################################

$(PREFIX1).autopgen.bin: $(AP_SRC2)/$(LANG2).autopgen.bin
	cp $< $@

$(PREFIX2).autopgen.bin: $(AP_SRC1)/$(LANG1).autopgen.bin
	cp $< $@

###############################################################################
## Distribution
###############################################################################

EXTRA_DIST=					\
		$(BASENAME).$(PREFIX1).lrx	\
		$(BASENAME).$(PREFIX1).dix	\
		$(BASENAME).$(PREFIX2).lrx	\
		$(BASENAME).$(PREFIX1).rtx	\
		$(BASENAME).$(PREFIX2).rtx	\
		modes.xml

###############################################################################
## Installation
###############################################################################

apertium_fin_fradir=$(prefix)/share/apertium/$(BASENAME)/

# Files which should be installed to $(prefix)/share/apertium/$(BASENAME)/:
apertium_fin_fra_DATA=	\
	$(TARGETS_COMMON)


# All modes are created by the same goal, listing several will just give problems with make -j2
noinst_DATA=modes/$(PREFIX1).mode \
	.deps/$(LANG1).automorf.hfst \
	.deps/$(LANG2).automorf.bin

install-data-local: install-modes
uninstall-local: uninstall-modes

###############################################################################
## Cleanup
###############################################################################

CLEANFILES = $(TARGETS_COMMON)
clean-local:
	-rm -rf .deps modes

###############################################################################
## Test
###############################################################################

check-local: test test-gen-fin-fra test-gen-fra-fin

test: all
	apertium-regtest test


# Slow but near-complete test of dictionary consistency:
test-gen-fin-fra: all
	! dev/testvoc/generation.sh --hfst fin-fra

test-gen-fra-fin: all
	! dev/testvoc/generation.sh fra-fin

test-voc: test-gen-fin-fra \
		  test-gen-fra-fin

