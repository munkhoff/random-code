#############################################################################
# 99 Bottles of Beer in GNU Make Script
# by John Munkhoff
#
# https://github.com/munkhoff/random-code/99-bottles/GNU-Make
#
# This Makefile prints out the lyrics of the song "99 Bottles of Beer"
# using the script facilities of the GNU "make" utility.
#
# It is a solution to a programming task on RosettaCode.org:
#   http://rosettacode.org/wiki/99_Bottles_of_Beer
#
# See also:
#   http://99-bottles-of-beer.net/
#
# Run "make" without any parameters to generate the lyrics.
# Run "make N-bottles" or "make N" to start with a different number of bottles.
# Run "make check" to automatically validate the program output.
#
#############################################################################
# This software is in the public domain.
#############################################################################

# Generate a list of 100 numeric strings in descending order,
# i.e. from 99 to 0. Leading zeros must be excluded for proper output.
digits:=9 8 7 6 5 4 3 2 1 0
numbers:=$(foreach x,$(filter-out 0,$(digits)),$(foreach y,$(digits),$x$y))
numbers+=$(digits)

# These string variables will be expanded when they are referenced in
# a command. They parse the target name and set the text accordingly.
bottles=bottle$(if $(findstring /1/,$@),,s)
num=$(if $(findstring /0/,$@),$(empty),$(@F))
action=$(if $(findstring /0/,$@),$(buy),$(pass))

# Some literal strings.
beer:=of beer
wall:=on the wall
empty:=No more
pass:=Take one down and pass it around.
buy:=Go to the store and buy some more.

# Define a function that generates two build targets for a number in the
# song. Only one of these targets is important; the other is just an alias
# for invoking it manually on the command line.
# For example: "make 12-bottles" or "make 12" will both work.
# 
# The actual printing of the text is handled by pattern rules; the target
# here only ensures those pattern rules are invoked in the proper order.
#
# The final item in the list of dependencies for this target is the target
# for the next (lower) number in the song. Invoking one target will
# automatically invoke all subsequent targets.
define verse

.PHONY: $1
$1: verse/$1/$1 prelude/$2/$2 $2

.PHONY: $1-bottles
$1-bottles: $1

# This variable will be set only once -- on the first invocation of
# this function.
most?=$1

endef

# Define a function that takes a string of numbers on input, calls the
# verse function with the first two numbers in that list, then strips
# off the first number from the list and makes a recursive call to
# itself. The recursion terminates when the list becomes empty.
define verses
$$(eval $$(call verse,$$(word 1,$1),$$(word 2,$1)))
$$(if $$(word 2,$1),$$(eval $$(call verses,$$(filter-out $$(word 1,$1),$1))))
endef

# Generate the targets for the 100 numbers.
$(eval $(call verses,$(numbers)))


# Main lines in the verse.
.PHONY: verse/%
verse/%:
	@echo "$(num) $(bottles) $(beer) $(wall)."
	@echo "$(num) $(bottles) $(beer)."
	@echo "$(action)"

# Last line of a verse, which is a prelude to the next verse.
.PHONY: prelude/%
prelude/%:
	@echo "$(num) $(bottles) $(beer) $(wall)."
	@echo ""

# Special target for the last line of the song.
.PHONY: prelude/
prelude/:
	@echo "$(most) $(bottles) $(beer) $(wall)!"
	@echo ""

# Special targets for the pedantic developer who considers "make 1-bottles"
# and "make 0-bottles" to be unnecessarily crude.
.PHONY: 1-bottle
1-bottle: 1

.PHONY: no-more
no-more: 0


# Automated test. Run "make check" to validate the program output.
log:=beer.log
.PHONY: check
check:
	@$(MAKE) > "$(log)"
	@test 500 -eq $$( wc -l "$(log)" | awk '{print $$1}' )
	@test 179 -eq $$( grep -c '^[1-9][0-9] bottles of beer on the wall\.' "$(log)" )
	@test  90 -eq $$( grep -c '^[1-9][0-9] bottles of beer\.' "$(log)" )
	@test  16 -eq $$( grep -c '^[2-9] bottles of beer on the wall\.' "$(log)" )
	@test   8 -eq $$( grep -c '^[2-9] bottles of beer\.' "$(log)" )
	@test   2 -eq $$( grep -c '^1 bottle of beer on the wall\.' "$(log)" )
	@test   1 -eq $$( grep -c '^1 bottle of beer\.' "$(log)" )
	@test   2 -eq $$( grep -c '^No more bottles of beer on the wall\.' "$(log)" )
	@test   1 -eq $$( grep -c '^No more bottles of beer\.' "$(log)" )
	@test   1 -eq $$( grep -c '^99 bottles of beer on the wall\!' "$(log)" )
	@test  99 -eq $$( grep -c '^Take one down and pass it around\.' "$(log)" )
	@test   1 -eq $$( grep -c '^Go to the store and buy some more\.' "$(log)" )
	@test 100 -eq $$( grep -c '^$$' "$(log)" )
	@echo "PASSED -- Please drink responsibly."

