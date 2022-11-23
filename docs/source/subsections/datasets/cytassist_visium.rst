==================================
CytAssist Human Lung Cancer (FFPE)
==================================

:Date: 2022-11-16

Dataset Explanation
===================

The human lung cancer (FFPE) dataset was obtained from 10x Genomics using their CytAssist Visium technology that has been recently developed to allow users to perform standard histology workflows on two standard glass slides before transferring the transcriptional probes on the two-area capture visium slide.

More information about this dataset can be found `here <https://www.10xgenomics.com/resources/datasets/human-lung-cancer-ffpe-2-standard>`_.

.. _here: https://www.10xgenomics.com/resources/datasets/human-lung-cancer-ffpe-2-standard

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/CytAssist%20Visium%20workflow.png?raw=true
   :width: 100.0%

Start Giotto
============

To run the current vignette you need to install the Giotto Suite branch.

.. container:: cell

   .. code:: r

      # Ensure Giotto Suite and GiottoData packages are installed
      if(!"Giotto" %in% installed.packages()) {
         devtools::install_github("drieslab/Giotto@Suite")
      }
      
      if(!"Giotto" %in% installed.packages()) {
         devtools::install_github("drieslab/GiottoData")
      }

      library(Giotto)
      library(GiottoData)
      
      # Ensure the Python environment for Giotto has been installed
      genv_exists = checkGiottoEnvironment()
      if(!genv_exists){
         # The following command need only be run once to install the Giotto environment.
         installGiottoEnvironment()
      }

      # to automatically save figures in save_dir set save_plot to TRUE
      temp_dir = getwd()
      myinstructions = createGiottoInstructions(save_dir = temp_dir,
                                          save_plot = TRUE,
                                          show_plot = TRUE)
                                          
1. Create a Giotto object
=========================

The minimum requirements are

-  matrix with expression information (or path to)
-  x,y(,z) coordinates for cells or spots (or path to)

.. container:: cell

   .. code:: r

      # Provide path to visium folder
      data_path = paste0(temp_dir, '/data/')
      getSpatialDataset(dataset = 'cytassist_lungcancer', directory = data_path, method = 'wget')

      # Create Giotto object
        visium_lungcancer = createGiottoVisiumObject(visium_dir = data_path,
                                                     expr_data = 'raw',
                                                     png_name = 'tissue_lowres_image.png',
                                                     gene_column_index = 2,
                                                     instructions = myinstructions)

      # check metadata
      pDataDT(visium_lungcancer)

      # check available image names
      showGiottoImageNames(visium_lungcancer) # "image" is the default name

      # show aligned image
      spatPlot(gobject = visium_lungcancer, cell_color = 'in_tissue', show_image = T, point_alpha = 0.7)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/0-spatPlot2D.png?raw=true
   :width: 50.0%

How to work with Giotto instructions that are part of your Giotto object:

-  show the instructions associated with your Giotto object with **showGiottoInstructions()**
-  change one or more instructions with **changeGiottoInstructions()**
-  replace all instructions at once with **replaceGiottoInstructions()**
-  read or get a specific Giotto instruction with **readGiottoInstructions()**

.. container:: cell

   .. code:: r

      # show instructions associated with giotto object (visium_lungcancer)
      showGiottoInstructions(visium_lungcancer)

2. Processing steps
===================

-  filter genes and cells based on detection frequencies
-  normalize expression matrix (log transformation, scaling factor
   and/or z-scores)
-  add cell and gene statistics (optional)
-  adjust expression matrix for technical covariates or batches
   (optional). These results will be stored in the *custom* slot.

.. container:: cell

   .. code:: r
      
      visium_lungcancer <- filterGiotto(gobject = visium_lungcancer,
                                        expression_threshold = 1,
                                        feat_det_in_min_cells = 50,
                                        min_det_feats_per_cell = 1000,
                                        expression_values = c('raw'),
                                        verbose = T)
      visium_lungcancer <- normalizeGiotto(gobject = visium_lungcancer, scalefactor = 6000, verbose = T)
      visium_lungcancer <- addStatistics(gobject = visium_lungcancer)
      

Visualize aligned tissue with number of features after processing
-----------------------------------------------------------------    

.. container:: cell

   .. code:: r
      
      spatPlot2D(gobject = visium_lungcancer, show_image = T, point_alpha = 0.7)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/1-spatPlot2D.png?raw=true
   :width: 50.0%

.. container:: cell

   .. code:: r
      
      spatPlot2D(gobject = visium_lungcancer, show_image = T, point_alpha = 0.7,
                 cell_color = 'nr_feats', color_as_factor = F)
      
.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/2-spatPlot2D.png?raw=true
   :width: 50.0%
   
3. Dimension Reduction
======================

-  identify highly variable features (HVF)

.. container:: cell

   .. code:: r

      visium_lungcancer <- calculateHVF(gobject = visium_lungcancer)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/3-HVFplot.png?raw=true
   :width: 50.0%

-  perform PCA
-  identify number of significant principal components (PCs)

.. container:: cell

   .. code:: r

      visium_lungcancer <- runPCA(gobject = visium_lungcancer)
      screePlot(visium_lungcancer, ncp = 30)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/4-screePlot.png?raw=true
   :width: 50.0%

.. container:: cell

   .. code:: r

      plotPCA(gobject = visium_lungcancer)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/5-PCA.png?raw=true
   :width: 50.0%

-  run UMAP and/or t-SNE on PCs (or directly on matrix)

.. container:: cell

   .. code:: r

      visium_lungcancer <- runUMAP(visium_lungcancer, dimensions_to_use = 1:10)
      plotUMAP(gobject = visium_lungcancer)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/6-UMAP.png?raw=true
   :width: 50.0%

.. container:: cell

   .. code:: r

      visium_lungcancer <- runtSNE(visium_lungcancer, dimensions_to_use = 1:10)
      plotTSNE(gobject = visium_lungcancer)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/7-tSNE.png?raw=true
   :width: 50.0%

4. Clustering
=============

-  create a shared (default) nearest network in PCA space (or directly on matrix)
-  cluster on nearest network with Leiden or Louvain (k-means and hclust are alternatives)

.. container:: cell

   .. code:: r

      # Create shared nearest network (SNN) and perform leiden clustering
      visium_lungcancer <- createNearestNetwork(gobject = visium_lungcancer, dimensions_to_use = 1:10, k = 30)
      visium_lungcancer <- doLeidenCluster(gobject = visium_lungcancer, spat_unit = 'cell', feat_type = 'rna', resolution = 0.4, n_iterations = 1000)

      # visualize UMAP cluster results
      plotUMAP(gobject = visium_lungcancer, cell_color = 'leiden_clus', show_NN_network = T, point_size = 2)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/8-UMAP.png?raw=true
   :width: 50.0%

.. container:: cell

   .. code:: r

      # visualize tSNE cluster results
      plotTSNE(gobject = visium_lungcancer, cell_color = 'leiden_clus', show_NN_network = T, point_size = 2)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/9-tSNE.png?raw=true
   :width: 50.0%
   
.. container:: cell

   .. code:: r

      # visualize expression and spatial results
      spatDimPlot(gobject = visium_lungcancer, cell_color = 'leiden_clus',
      dim_point_size = 2, spat_point_size = 2)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/12-spatDimPlot2D.png?raw=true
   :width: 50.0%
   
.. container:: cell

   .. code:: r

      spatDimPlot(gobject = visium_lungcancer, cell_color = 'nr_feats', color_as_factor = F,
      dim_point_size = 2, dim_show_legend = T, spat_show_legend = T, spat_point_size = 2)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/13-spatDimPlot2D.png?raw=true
   :width: 50.0%   
   
5. Differential expression
==========================

.. container:: cell

   .. code:: r

      # Cell type marker detection
      # Gini markers
      gini_markers_subclusters = findMarkers_one_vs_all(gobject = visium_lungcancer,
                                                        method = 'gini',
                                                        expression_values = 'normalized',
                                                        cluster_column = 'leiden_clus',
                                                        min_featss = 20,
                                                        min_expr_gini_score = 0.5,
                                                        min_det_gini_score = 0.5)

      # get top 2 genes per cluster and visualize with violin plot
      topgenes_gini = gini_markers_subclusters[, head(.SD, 2), by = 'cluster']$feats
      violinPlot(visium_lungcancer, feats = unique(topgenes_gini), cluster_column = 'leiden_clus',
                 strip_text = 8, strip_position = 'right')

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/14-violinPlot.png?raw=true
   :width: 50.0%  

.. container:: cell

   .. code:: r

      # cluster heatmap
      plotMetaDataHeatmap(visium_lungcancer,
                          selected_feats = topgenes_gini,
                          metadata_cols = c('leiden_clus'),
                          x_text_size = 10, y_text_size = 10)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/15-plotMetaDataHeatmap.png?raw=true
   :width: 50.0%  

.. container:: cell

   .. code:: r

      # umap plots
      dimFeatPlot2D(visium_lungcancer,
                    expression_values = 'scaled',
                    feats = gini_markers_subclusters[, head(.SD, 1), by = 'cluster']$feats,
                    cow_n_col = 3, point_size = 1)   


.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/16-dimFeatPlot2D.png?raw=true
   :width: 50.0%  
   
.. container:: cell

   .. code:: r
   
      # Cell type marker detection
      # Scran markers
      scran_markers_subclusters = findMarkers_one_vs_all(gobject = visium_lungcancer,
                                                         method = 'scran',
                                                         expression_values = 'normalized',
                                                         cluster_column = 'leiden_clus')

      # get top 2 genes per cluster and visualize with violin plot
      topgenes_scran = scran_markers_subclusters[, head(.SD, 2), by = 'cluster']$feats
      violinPlot(visium_lungcancer, feats = unique(topgenes_scran),
                 cluster_column = 'leiden_clus',
                 strip_text = 10, strip_position = 'right')
                 
.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/17-violinPlot.png?raw=true
   :width: 50.0% 
   
.. container:: cell

   .. code:: r
   
      # cluster heatmap
      plotMetaDataHeatmap(visium_lungcancer,
                          selected_feats = topgenes_scran,
                          metadata_cols = c('leiden_clus'),
                          x_text_size = 10, y_text_size = 10)   

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/18-plotMetaDataHeatmap.png?raw=true
   :width: 50.0% 
   
.. container:: cell

   .. code:: r
   
      # umap plots
      dimFeatPlot2D(visium_lungcancer,
                    expression_values = 'scaled',
                    feats = scran_markers_subclusters[, head(.SD, 1), by = 'cluster']$feats,
                    cow_n_col = 3, point_size = 1)
                    
6. Cell Type Enrichment
=======================

| Visium spatial transcriptomics does not provide single-cell
  resolution, making cell type annotation a harder problem. Giotto
  provides several ways to calculate enrichment of specific cell-type
  signature gene lists:
| - PAGE
| - hypergeometric test
| - Rank
| - `DWLS
  Deconvolution <https://genomebiology.biomedcentral.com/articles/10.1186/s13059-021-02362-7>`__ Corresponded Single cell dataset can be generated from
  `here <http://mousebrain.org/>`__. 
  Giotto_SC is processed from the
  downsampled `Loom <https://satijalab.org/loomr/loomr_tutorial>`__ file and can also be downloaded from getSpatialDataset.
  
PAGE 
----
The cell-type specific signature gene list was obtained from a previous study focused on investigating the therapy-induced evolution of lung cancer revealed by single-cell RNA sequencing. More information about the paper and scRNA-seq dataset can be found `here <https://doi.org/10.1016/j.cell.2020.07.017>`_.

.. _here: https://doi.org/10.1016/j.cell.2020.07.017

.. container:: cell

   .. code:: r
   
      # umap plots
      # Create PAGE matrix
      # PAGE matrix should be a binary matrix with each row represent a gene marker and each column represent a cell type
      # There are several ways to create PAGE matrix
      # 1.1 create binary matrix of cell signature genes
      # small example #
      Tcells_markers = c("CD2", "CD3D", "CD3E", "CD3G")
      macrophage_markers = c("MARCO", "CSF1R", "CD68", "GLDN", "APOE", "CCL3L1", "TREM2", "C1QB", "NUPR1", "FOLR2", "RNASE1", "C1QA")
      dendritic_markers = c("CD1E", "CD1C", "FCER1A", "PKIB", "CYP2S1", "NDRG2")
      mast_markers = c("CMA1", "TPSAB1", "TPSB2")
      Bcell_markers = c("IGLL5", "MZB1", "JCHAIN", "DERL3", "SDC1", "MS$A1", "BANK1", "PAX5", "CD79A")
      Bcell_PB_markers = c("PRDM1", "XSP1", "IRF4")
      Bcell_mem_markers = c("MS4A1", "IRF8")
      housekeeping_markers = c("ACTB", "GAPDH", "MALAT1")
      neutrophils_markers = c("FCGR3B", "ALPL", "CXCR1", "CXCR2", "ADGRG3", "CMTM2", "PROK2", "MME", "MMP25", "TNFRSF10C")
      pdcs_markers = c("SLC32A1", "SHD", "LRRC26", "PACSIN1", "LILRA4", "CLEC4C", "DNASE1L3", "SCT", "LAMP5")

      signature_matrix = makeSignMatrixPAGE(sign_names = c('T_Cells', 'Macrophage', 'Dendritic', 'Mast', 'B_cell', 'Bcell_PB', 'Bcells_memory',
      'Housekeeping', 'Neutrophils', 'pDCs'),
       sign_list = list(Tcells_markers,
                 macrophage_markers,
                 dendritic_markers,
                 mast_markers,
                 Bcell_markers,
                 Bcell_PB_markers,
                 Bcell_mem_markers,
                 housekeeping_markers,
                 neutrophils_markers,
                 pdcs_markers))

      # 1.3 enrichment test with PAGE

      markers_scran = findMarkers_one_vs_all(gobject=giotto_SC, method="scran",
                                              expression_values="normalized", cluster_column = "Class", min_feats=3)

      top_markers <- markers_scran[, head(.SD, 10), by="cluster"]
      celltypes<-levels(factor(markers_scran$cluster))
      sign_list<-list()
      for (i in 1:length(celltypes)){
      sign_list[[i]]<-top_markers[which(top_markers$cluster == celltypes[i]),]$feats
      }

      PAGE_matrix_3 = makeSignMatrixPAGE(sign_names = celltypes,
                             sign_list = sign_list)

      #  runSpatialEnrich() can also be used as a wrapper for all currently provided enrichment options
      visium_lungcancer = runPAGEEnrich(gobject = visium_lungcancer, sign_matrix = signature_matrix, min_overlap_genes = 1)

      # 1.4 heatmap of enrichment versus annotation (e.g. clustering result)
      cell_types = colnames(signature_matrix)
      plotMetaDataCellsHeatmap(gobject = visium_lungcancer,
                                metadata_cols = 'leiden_clus',
                                value_cols = cell_types,
                                spat_enr_names = 'PAGE',
                                x_text_size = 8,
                                y_text_size = 8,
                                show_plot = T,
                                save_param = list(save_name="7_a_metaheatmap"))

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/7_a_metaheatmap.png?raw=true
   :width: 50.0% 
   
.. container:: cell

   .. code:: r
   
      cell_types_subset = colnames(signature_matrix)
      spatCellPlot(gobject = visium_lungcancer,
                  spat_enr_names = 'PAGE',
                  cell_annotation_values = cell_types_subset,
                  cow_n_col = 4, coord_fix_ratio = NULL, point_size = 0.75,
                  save_param = list(save_name="7_b_spatcellplot_1"))
   
.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/7_b_spatcellplot_1.png?raw=true
   :width: 50.0%                                 
   
.. container:: cell

   .. code:: r
   
      spatDimCellPlot(gobject = visium_lungcancer,
                      spat_enr_names = 'PAGE',
                      cell_annotation_values = c('B_cell','Macrophage'),
                      cow_n_col = 1, spat_point_size = 1.2,
                      plot_alignment = 'horizontal',
                      save_param = list(save_name="7_d_spatDimCellPlot", base_width=7, base_height=10))  
                      
.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/7_d_spatDimCellPlot.png?raw=true
   :width: 50.0%                         

7. Spatial Grids 
================

.. container:: cell

   .. code:: r
   
      visium_lungcancer <- createSpatialGrid(gobject = visium_lungcancer,
                             sdimx_stepsize = 400,
                             sdimy_stepsize = 400,
                             minimum_padding = 0)

     spatPlot(visium_lungcancer, cell_color = 'leiden_clus', point_size = 2.5, show_grid = T,
     grid_color = 'red', spatial_grid_name = 'spatial_grid')

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/21-spatPlot2D.png?raw=true
   :width: 50.0%

8. Spatial Network
==================

.. container:: cell

   .. code:: r
   
      ## Delaunay network: stats + creation
      plotStatDelaunayNetwork(gobject = visium_lungcancer, maximum_distance = 400)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/22-plotStatDelaunayNetwork.png?raw=true
   :width: 50.0%
   
.. container:: cell

   .. code:: r
   
      visium_lungcancer = createSpatialNetwork(gobject = visium_lungcancer, minimum_k = 0)
      showNetworks(visium_lungcancer)
      spatPlot(gobject = visium_lungcancer, show_network = T,
      network_color = 'blue', spatial_network_name = 'Delaunay_network')
      
.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/23-spatPlot2D.png?raw=true
   :width: 50.0%
   
9. Spatial Genes
================
   
.. container:: cell

   .. code:: r  
   
      # kmeans binarization
      kmtest = binSpect(visium_lungcancer)
      spatFeatPlot2D(visium_lungcancer, expression_values = 'scaled',
                      feats = kmtest$feats[1:6], cow_n_col = 2, point_size = 1.5)
                      
.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/24-spatFeatPlot2D.png?raw=true
   :width: 50.0%
   
.. container:: cell

   .. code:: r  
   
      ## rank binarization
      ranktest = binSpect(visium_lungcancer, bin_method = 'rank')
      spatFeatPlot2D(visium_lungcancer, expression_values = 'scaled',
                      feats = ranktest$feats[1:6], cow_n_col = 2, point_size = 1.5)
   
.. container:: cell

   .. code:: r
   
      ## spatially correlated genes ##
      ext_spatial_genes = kmtest[1:500]$feats

      # 1. calculate gene spatial correlation and single-cell correlation
      # create spatial correlation object
      spat_cor_netw_DT = detectSpatialCorFeats(visium_lungcancer,
                                                method = 'network',
                                                spatial_network_name = 'Delaunay_network',
                                                subset_feats = ext_spatial_genes)

      # 2. identify most similar spatially correlated genes for one gene
      DNAI1_top10_genes = showSpatialCorFeats(spat_cor_netw_DT, feats = 'DNAI1', show_top_feats = 10)

      spatFeatPlot2D(visium_lungcancer, expression_values = 'scaled',
                      feats = c('RSPH1', 'C20orf85', 'DNAAF1','TEKT2'), point_size = 3)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/25-spatFeatPlot2D.png?raw=true
   :width: 50.0%   
   
.. container:: cell

   .. code:: r
   
      spatFeatPlot2D(visium_lungcancer, expression_values = 'scaled',
                      feats = c('TEKT2', 'CFAP157', 'MAPK15', 'MS4A8', 'CDHR3', 'C9orf24'), point_size = 3)
                      
.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/26-spatFeatPlot2D.png?raw=true
   :width: 50.0%

.. container:: cell

   .. code:: r
      
      # 3. cluster correlated genes & visualize 
      spat_cor_netw_DT = clusterSpatialCorFeats(spat_cor_netw_DT, name = ‘spat_netw_clus’, k = 10)
      
      heatmSpatialCorFeats(visium_lungcancer, spatCorObject = spat_cor_netw_DT, use_clus_name = ‘spat_netw_clus’,
      save_param = c(save_name = ‘22-z1-heatmap_correlated_genes’, save_format = ‘pdf’, base_height = 6, base_width = 8, units = ‘cm’), heatmap_legend_param = list(title = NULL))
      
.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/22-z1-heatmap_correlated_genes.png?raw=true
   :width: 50.0%
   
.. container:: cell

   .. code:: r
      
      # 4. rank spatial correlated clusters and show genes for selected clusters
      netw_ranks = rankSpatialCorGroups(visium_lungcancer, spatCorObject = spat_cor_netw_DT, use_clus_name = 'spat_netw_clus',
                                        save_param = c(save_name = '22-z2-rank_correlated_groups',
                                           base_height = 3, base_width = 5))

      top_netw_spat_cluster = showSpatialCorFeats(spat_cor_netw_DT, use_clus_name = 'spat_netw_clus',
                                                  selected_clusters = 6, show_top_feats = 1) 
                                                  
.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/22-z2-rank_correlated_groups.png?raw=true
   :width: 50.0%                                                  

.. container:: cell

   .. code:: r
      
      # 5. create metagene enrichment score for clusters
      cluster_genes_DT = showSpatialCorFeats(spat_cor_netw_DT, use_clus_name = 'spat_netw_clus', show_top_feats = 1)
      cluster_genes = cluster_genes_DT$clus; names(cluster_genes) = cluster_genes_DT$feat_ID

      visium_lungcancer = createMetafeats(visium_lungcancer, feat_clusters = cluster_genes, name = 'cluster_metagene')

      showGiottoSpatEnrichments(visium_lungcancer)

      spatCellPlot(visium_lungcancer,
                    spat_enr_names = 'cluster_metagene',
                    cell_annotation_values = netw_ranks$clusters,
                    point_size = 1.5, cow_n_col = 4)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/30-spatCellPlot2D.png?raw=true
   :width: 50.0%                                                  

10. HMRF Domains
================

.. container:: cell

   .. code:: r
   
      # HMRF requires a fully connected network!
      visium_lungcancer = createSpatialNetwork(gobject = visium_lungcancer, minimum_k = 2, name = 'Delaunay_full')

      # spatial genes
      my_spatial_genes <- kmtest[1:100]$feats

      # do HMRF with different betas
      hmrf_folder = paste0(results_folder,'/','HMRF_results/')
      if(!file.exists(hmrf_folder)) dir.create(hmrf_folder, recursive = T)

      # if Rscript is not found, you might have to create a symbolic link, e.g.
      # cd /usr/local/bin
      # sudo ln -s /Library/Frameworks/R.framework/Resources/Rscript Rscript
      HMRF_spatial_genes = doHMRF(gobject = visium_lungcancer,
                            expression_values = 'scaled',
                            spatial_network_name = 'Delaunay_full',
                            spatial_genes = my_spatial_genes,
                            k = 5,
                            betas = c(0, 10, 3),
                            output_folder = paste0(hmrf_folder, '/', 'Spatial_genes/SG_topgenes_k5_scaled'))

      ## alternative way to view HMRF results
      # results = writeHMRFresults(gobject = ST_test,
      #                           HMRFoutput = HMRF_spatial_genes,
      #                           k = 5, betas_to_view = seq(0, 25, by = 5))
      # ST_test = addCellMetadata(ST_test, new_metadata = results, by_column = T, column_cell_ID = 'cell_ID')

      ## add HMRF of interest to giotto object
      visium_lungcancer = addHMRF(gobject = visium_lungcancer,
                        HMRFoutput = HMRF_spatial_genes,
                        k = 5, betas_to_add = c(0,10,20),
                        hmrf_name = 'HMRF')

      showGiottoSpatEnrichments(visium_lungcancer)

      ## visualize
      spatPlot(gobject = visium_lungcancer, cell_color = 'HMRF_k5_b.0', point_size = 3)

.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/27-spatPlot2D.png?raw=true
   :width: 50.0%    

.. container:: cell

   .. code:: r
   
      spatPlot(gobject = visium_lungcancer, cell_color = 'HMRF_k5_b.10', point_size = 3)
      
.. image:: https://github.com/PratishthaGuckhool/Giotto_site_suite/blob/master/inst/images/cytassist_visium_lungcancer/28-spatPlot2D.png?raw=true
   :width: 50.0% 
