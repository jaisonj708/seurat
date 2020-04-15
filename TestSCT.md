## Test SCTransform split.by 
```R
InstallData("panc8")
# rm(list=ls()[!ls() %in% c('panc8','cells')])
data("panc8")

cells <- which(x = panc8[["tech", drop = TRUE]] != "indrop") # exclude indrop
cells <- colnames(x = panc8)[cells]
panc8 <- subset(x = panc8, cells = cells)

panc8.transformed <- SCTransform(panc8, split.by = "tech", verbose = TRUE)

panc8.list <- SplitObject(panc8.transformed, split.by = "tech")
panc8.list <- panc8.list[c("celseq", "celseq2", "fluidigmc1", "smartseq2")]
panc8.features <- SelectIntegrationFeatures(object.list = panc8.list, nfeatures = 3000)
panc8.anchors <- FindIntegrationAnchors(object.list = panc8.list, normalization.method = "SCT", anchor.features = panc8.features, verbose = TRUE)
panc8.integrated <- IntegrateData(anchorset = panc8.anchors, normalization.method = "SCT", verbose = TRUE)

panc8.integrated <- RunPCA(panc8.integrated, verbose = FALSE)
panc8.integrated <- RunUMAP(panc8.integrated, dims = 1:30)
plots <- DimPlot(panc8.integrated, group.by = c("tech", "celltype"), pt.size = 0.03)
plots & theme(legend.position = "top") & guides(color = guide_legend(nrow = 3, byrow = TRUE, 
                                                                     override.aes = list(size = 3)))
```
