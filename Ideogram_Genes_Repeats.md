# R commands for Plot Ideogram containing genes and  repeats density 


# 1. Libraries


      library(IRanges)
      require(RIdeogram)
      library(ggplot2)
      require(ggideogram)
      library(ggnewscale)



# 2.  Genes annotation

      gene_density1 <- GFFex(input = "/Users/mateusgo/Documents/2024_FRIBURG2/02_2024_ArganGenome/03_Manuscript_v6/04_Annotation/01_hap1/Sspinosum_hap1.gtf", karyotype = "/Users/mateusgo/Documents/2024_FRIBURG2/02_2024_ArganGenome/03_Manuscript_v6/11_Ideogram/Hap1_Karyotype.txt", feature = "gene", window = 1000000)
      gene_density2 <- GFFex(input = "/Users/mateusgo/Documents/2024_FRIBURG2/02_2024_ArganGenome/03_Manuscript_v6/04_Annotation/02_hap2/Sspinosum_hap2.gtf", karyotype = "/Users/mateusgo/Documents/2024_FRIBURG2/02_2024_ArganGenome/03_Manuscript_v6/11_Ideogram/Hap2_Karyotype.txt", feature = "gene", window = 1000000)

      gene_density1$Chr <- factor(gene_density1$Chr , levels = c("Chromosome_1","Chromosome_2","Chromosome_3","Chromosome_4","Chromosome_5","Chromosome_6","Chromosome_7","Chromosome_8","Chromosome_9","Chromosome_10","Chromosome_11"))

      gene_density2$Chr <- factor(gene_density2$Chr , levels = c("Chromosome_1","Chromosome_2","Chromosome_3","Chromosome_4","Chromosome_5","Chromosome_6","Chromosome_7","Chromosome_8","Chromosome_9","Chromosome_10","Chromosome_11"))






# 3. Repeats annotations

## Hap1

      Repeatshap1<- read.delim("/Users/mateusgo/Documents/2024_FRIBURG2/02_2024_ArganGenome/03_Manuscript_v6/03_Repeats/S_spinosum_hap1.fa.out.gff",h=F,comment.char = "#",sep="\t")
      colnames(Repeatshap1)<-c("Contig","Repeatmasker","Type","Start","End","V6","Strand","V8","Info")

      Repeatshap1$Contig<-factor(Repeatshap1$Contig, levels = c("Chromosome_1","Chromosome_2","Chromosome_3", "Chromosome_4","Chromosome_5","Chromosome_6","Chromosome_7","Chromosome_8","Chromosome_9", "Chromosome_10", "Chromosome_11","scaffold_12", "scaffold_13","scaffold_14"))


      RepeatIDhap1<-lapply(strsplit(Repeatshap1$Info,split =" "),function (x) x [1] ) # extract ID
      RepeatIDhap1<-lapply(strsplit(unlist(RepeatIDhap1), split = ";"),function (x) x[1])
      RepeatIDhap1<-as.numeric(gsub("ID=","",unlist(RepeatIDhap1)))
      RepeatMotifhap1<-lapply(strsplit(Repeatshap1$Info,split =" "),function (x) x [2] ) # extract ID info
      RepeatMotifhap1<-gsub("Motif:","",unlist(RepeatMotifhap1))
      Repeatshap1<-cbind.data.frame(Repeatshap1,ID=RepeatIDhap1, Motif=RepeatMotifhap1) # ADD columns



## Hap2

      Repeatshap2<- read.delim("/Users/mateusgo/Documents/2024_FRIBURG2/02_2024_ArganGenome/03_Manuscript_v6/03_Repeats/Repeats_S_spinosum_hap2.fa.out.gff",h=F,comment.char = "#",sep="\t")
      colnames(Repeatshap2)<-c("Contig","Repeatmasker","Type","Start","End","V6","Strand","V8","Info")

      Repeatshap2$Contig<-factor(Repeatshap2$Contig, levels = c("Chromosome_1","Chromosome_2","Chromosome_3", "Chromosome_4","Chromosome_5","Chromosome_6","Chromosome_7","Chromosome_8","Chromosome_9", "Chromosome_10", "Chromosome_11","scaffold_12", "scaffold_13","scaffold_14","scaffold_15","scaffold_16","scaffold_17","scaffold_18","scaffold_19","scaffold_20"))#order contigs


      RepeatIDhap2<-lapply(strsplit(Repeatshap2$Info,split =" "),function (x) x [1] ) # extract ID  
      RepeatIDhap2<-lapply(strsplit(unlist(RepeatIDhap2), split = ";"),function (x) x[1])  
      RepeatIDhap2<-as.numeric(gsub("ID=","",unlist(RepeatIDhap2)))
      
      RepeatMotifhap2<-lapply(strsplit(Repeatshap2$Info,split =" "),function (x) x [2] ) # extract ID info
      RepeatMotifhap2<-gsub("Motif:","",unlist(RepeatMotifhap2))
      Repeatshap2<-cbind.data.frame(Repeatshap2,ID=RepeatIDhap2, Motif=RepeatMotifhap2)# ADD columns


      repo<-Repeatshap1[grep("scaffold",Repeatshap1$Contig,invert = T),]


# 3b. Calculate nb bases that are repeats in 1Mb window
### Define window size

      window_size <- 1e6

### Assign window start

      repo$WindowStart <- floor(repo$Start / window_size) * window_size

### Create unique ID for each contig-window

      repo$Group <- paste(repo$Contig, repo$WindowStart, sep = ":")

### Split the data by contig-window group
      
      groups <- split(repo, repo$Group)

### Compute non-overlapping repeat length for each group

      get_nonoverlapping_length <- function(subdf) {
        ir <- IRanges(start = subdf$Start, end = subdf$End)
        sum(width(reduce(ir)))      }

### Apply and collect results

      results <- lapply(names(groups), function(k) {
        parts <- strsplit(k, ":")[[1]]
        contig <- parts[1]
        win_start <- as.numeric(parts[2])
        repeat_length <- get_nonoverlapping_length(groups[[k]])
        data.frame(Contig = contig, Window = win_start, RepeatLength = repeat_length)      })

      density_df <- do.call(rbind, results)

### Calculate percentage and Mb coordinates

      density_df$Percent <- (density_df$RepeatLength / window_size) * 100
      density_df$WindowMb <- density_df$Window / 1e6

### Ensure full coverage of windows per contig

      all_windows <- do.call(rbind, lapply(split(density_df, density_df$Contig), function(subdf) { 
      data.frame(
    Contig = unique(subdf$Contig),
    Window = seq(min(subdf$Window), max(subdf$Window), by = window_size)
        )
      }))

      density_df <- merge(all_windows, density_df, by = c("Contig", "Window"), all.x = TRUE)
      density_df$RepeatLength[is.na(density_df$RepeatLength)] <- 0
      density_df$Percent[is.na(density_df$Percent)] <- 0
      density_df$WindowMb <- density_df$Window / 1e6

### reorder windows table

      df_ordered <- density_df[order(density_df$Window), ]
      df_ordered <- df_ordered[order(df_ordered$Contig), ]

      endbin<-c((df_ordered$Window -1 ),36999999)
      endbin<-endbin [-1]
      df_ordered$end<- endbin
      df_ordered_v2<-df_ordered[,c(1,2,6,4)]

      colnames(df_ordered_v2)<-c("Chr","Start","End","Value")

### force all values higher than 100 to 100

      df_ordered_v2$Value[df_ordered_v2$Value>100]<-100

### correct -1 error
      
      df_ordered_v2[df_ordered_v2[, 3] == -1, 3] <- df_ordered_v2[df_ordered_v2[, 3] == -1, 3 - 1] + 1000000


# 4. plot


            ggplot() + geom_ideogram(aes(x = Chr, ymin = Start, ymax = End, 
                    chrom = Chr, fill = Value), 
                data = gene_density1,
                radius = unit(4, 'pt'), width = 0.4, 
                linewidth = 1, just = -0.05,
                chrom.col = "#252525", chrom.lwd = .5) +
              scale_fill_gradient(name = "gene_density", low = "white", 
                      high = "turquoise4", n.breaks = 3) +
              ggnewscale::new_scale_fill() +
              geom_ideogram(aes(x = Chr, ymin = Start, ymax = End, 
                    chrom = Chr, fill = Value), 
                data = df_ordered_v2,
                radius = unit(4, 'pt'), width = 0.4, 
                linewidth = 1, just = 1.05,
                chrom.col = "#252525", chrom.lwd = .5) +
              scale_fill_gradient(name = "repeat_density", low = "white", 
                      high = "firebrick", n.breaks = 3, limit = c(0,100)) +
              theme(
          axis.title = element_blank(),
          axis.text.y = element_blank(),
          axis.text.x = element_text(angle = 90, hjust = 1),
          axis.ticks = element_blank(),
          panel.background = element_blank(),
          legend.position = 'top' )

