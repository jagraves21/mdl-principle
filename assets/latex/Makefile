# TEX Sources
TEX_SOURCES := universal-prefix-tree.tex code-space-tree.tex shannon-fano-code.tex $(wildcard lexicographical-code-construction-*.tex)
PDFS := $(TEX_SOURCES:.tex=.pdf)

# PDF Sources
PNG_SOURCES := universal-prefix-tree.tex code-space-tree.tex shannon-fano-code.tex
PNGS := $(PNG_SOURCES:.tex=.png)

# GIF sources
GIF_SOURCES := $(wildcard lexicographical-code-construction-*.tex)
GIF_PDFS := $(GIF_SOURCES:.tex=.pdf)
GIF_FRAMES := $(patsubst %.pdf,frame_%.png,$(GIF_PDFS))
GIF := lexicographical-code-construction.gif

# Default target
all: $(PNGS) $(GIF)

# Build PDFs only from TEX_SOURCES
$(PDFS): %.pdf: %.tex
	pdflatex -interaction=nonstopmode $<

# Build PNGs only from selected sources
$(PNGS): %.png: %.pdf
	magick -density 300 $< -colorspace Gray -resize 75% -quality 100 $@

# Build GIF frames from lexicographical-code-construction PDFs (high-res grayscale)
$(GIF_FRAMES): frame_%.png : %.pdf
	magick -density 300 $< -colorspace Gray -resize 75% -quality 100 $@

# Combine frames into GIF
$(GIF): $(GIF_FRAMES)
	magick -delay 100 $(GIF_FRAMES) -loop 0 -dispose Background $@

# Build PDFs from TEX sources if updated
pdf: $(PDFS)

# Build PNGs from TEX sources if updated
png: $(PNGS)

# Build frames for the GIF TEX sources if updated
frames: $(GIF_FRAMES)

# Build GIF from TEX sources if updated
gif: $(GIF)

# Moves PNGs and GIF to ../images
install: $(PNGS) $(GIF)
	mkdir -p ../images
	cp $(PNGS) $(GIF) ../images/

# Clean LaTeX auxiliary files
clean-latex:
	rm -f *.aux *.log *.toc *.out *.lof *.lot *.fls *.fdb_latexmk *.synctex.gz

# Clean GIF frames
clean-frames:
	rm -f $(GIF_FRAMES)

# Standard clean calls LaTeX and frames cleanup
clean: clean-latex clean-frames

# Clean PDFs, PNGs, GIF frames, and GIF
veryclean: clean-latex clean-frames
	rm -f $(PDFS) $(PNGS) $(GIF)

# Phony targets
.PHONY: all clean veryclean pdf png frames gif clean-latex clean-frames clean-all

