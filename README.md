# Rev_scripts_MutEffect

## Credit: Dr. Su-Ping Deng and Haoran Chen

Reorganized Haoran's scripts in Mutational_Efffect including splitting, deleting, and wrapping etc.  
Pipeline and instructions (modified)  

## STEP 1: network transformation
	* Input: PPI_sig_network  
        * Code:  net_trans.r  
        * Output: wang_network.txt, PPI_sig_network_no_selfloop.txt  
	    
## STEP 2: Identifying motif 
	* Input: wang_network.txt  
	* Code: find_motif.sh (MFINDER 1.21)  
	* Output: PPI_sig_network_MEMBERS.txt, PPI_sig_network_OUT.txt  
	
## STEP 3: Process motif  
	* Input: PPI_sig_network_MEMBERS.txt, PPI_sig_network_OUT.txt  
	* Code: proc_motif.r  
	* Output: motif.txt, motif_subclass.txt  
	
## STEP 4: Statistical analysis of motifs to find hot motifs  
	* Input: PPI_sig_network_no_selfloop.txt, Census.csv, motif  
	* Code: stat_motif.r  
 	* Output: hot_motif_stat.txt    
	
## STEP 5: Generate feature matrix (based on motif)  
	* Input: Census.csv, motif_subclass.txt  
	* Code: gen_fea_matirx_SMOTE.r  
	* Output: feature_original1.txt (for python), feature_original.txt （for R) (just output feature_matrix.txt; when used in python, can be first processed in bash and save as feature_matrix_py.txt)  
	
## STEP 6: Random forest in python to get the optimal number of decision trees  
	* Input: feature_matrix_py.txt   
	* Code: RandomForest.py  
	* Output: RandomForest.joblib.pkl (RandomForest model)  
	
## STEP 7: Rule extraction by random forest and sparse group lasso in R  
	* Input: feature_matrix.txt  
	* Code: RF_SGL.r (decision_path.r,  discretize.r,  Optimization.r,  Prediction.r,  Recover_Validation.r,   ROC.r,  Rule_RScore.r)   
	Should be changed based on whole data instead of just test data  
	* Output: RsResult.RData, Prediction_Result.RData, rule_extract.RData  
	
## STEP 8: Find overlap motifs between those by statistical analysis and those by rule extraction  
	* Input: rule_extract.RData, hot_motif_stat.txt  
	* Code: overlap_hot_motif.r  
	* Output: overlap_hot.motif.txt  
	
## STEP 9: Draw figure of motif scores by rule extraction  
	* Input: motif, rule_extract.RData  
	* Code: draw_motif_score.r  
	* Output: motif_score.eps  
	
## STEP 10: Plot precision_recall curve, AUC_ROC curve  
	* Input: X_test.txt, label_test.txt, RandomForest.joblib.pkl  
	* Code: Plot_PRC_ROC.py  
	* Output: model_RandomForest.best_estimator.txt, helpout_ROC.eps, helpout_PC.eps  
	
## STEP 11: Compare with centrality-based features  
	### STEP 11.1 Generate centrality-based feature matrix  
		* Input: PPI_sig_network, feature_label_motif.txt  
		* Code: gen_fea_cen.r  
		* Output: feature_label_cen.txt  
	### STEP 11.2 Generate centrality-based random forest model  
		* Input: feature_label_cen.txt  
		* Code: RandomForest_new.py  
		* Output: /RandomForest_cen.joblib.pkl, X_test_cen.txt  
	### STEP 11.3 Plot figures to compare two types of features  
		* Input: X_test_motif.txt,  X_test_cen.txt,  label_test.txt, RandomForest_motif.joblib.pkl,   RandomForest_cen.joblib.pkl  
		* Code: plot_curve_cmp.py  
		* Output: heldout_ROC_cmp.eps, heldout_PC_cmp.eps  
