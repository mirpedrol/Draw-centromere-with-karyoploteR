
## Draw centromere with karyoploteR

[Preview](http://htmlpreview.github.io/?https://github.com/asthara10/Draw-centromere-with-karyoploteR/blob/main/karyoploteR_centromere.html) of the html file with instructions.

karyoploteR is an R package to create karyoplots. A chromosome ideogram can be plotted easily with two code lines.
```R
library(karyoploteR)
kp <- plotKaryotype(plot.type=6)
```
![Basic plot](/Images/basicplot.png)

Cytobands are drawn with the default “circos” color scheme, where centromeres are painted in red.

One of the most common representations of karyoplotes consists on drawing a constriction at the centromere. We can use some graphical tricks in order to plot the chromosome shapes.
```R
library(karyoploteR)
library(dplyr)
# Retrieve default cytobands and remove centromeres
cytobands <- getCytobands(genome="hg38")
cytobands.new <- GRanges(as.data.frame(cytobands[cytobands$gieStain != "acen"]))
# Retrieve centromere positions
centromeres <- as.data.frame(cytobands[cytobands$gieStain == "acen"])
centromeres <- as.data.frame(centromeres %>% dplyr::group_by(seqnames) %>% dplyr::summarize(x0=min(start), x1=max(end)))
chr <- length(centromeres$seqnames)
# Increase margin between crhomosomes (optional)
plot.params <- getDefaultPlotParams(plot.type=6)
plot.params$data1inmargin <- plot.params$data1inmargin + 50
# Initialize the karyoplote using modified plot.params and cytobands
kp <- plotKaryotype(plot.type=6, plot.params = plot.params, cytobands = cytobands.new)
# Draw centromeres
kpSegments(kp, chr=rep(centromeres$seqnames, 2), x0=rep(centromeres$x0, 2), x1=rep(centromeres$x1, 2), y0=c(rep(0,chr), rep(1,chr)), y1=c(rep(1,chr), rep(0,chr)))
```
![Centromere plot](/Images/centromereplot.png)
