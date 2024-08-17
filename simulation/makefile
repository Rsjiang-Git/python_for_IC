
# TEST            := rkv_i2c_master_directed_interrupt_test

module_name   := i2c
PRJ_HOME      := $(shell echo $(CURDIR) | sed -r 's|\/$(module_name)\/.*|\/$(module_name)|')
export PRJ_HOME

FILELIST_HOME := /mnt/hgfs/my_share/ic_prj/examples/uvm_template/$(module_name)
export FILELIST_HOME 

SEED          := $(shell head -4 /dev/urandom | od -N 4 -D -A n |awk '{gsub(/ /,"")}1')


cm            := -cm line+cond+tgl+fsm+branch+assert
cm_name       := -cm_name $(TEST)_$(SEED)
cm_dir        := -cm_dir  $(PRJ_HOME)/vdb/$(TEST)/simv.vdb

merge_cov_file  := $(shell find $(PRJ_HOME)/vdb -name "*.vdb")

test:
	echo $(PRJ_HOME)



compile_option   := 	vcs  \
        	-kdb -lca  \
        	-full64 \
        	-sverilog +v2k \
        	-noIncrComp \
        	-timescale=1ns/1ps \
        	+lint=TFIPC-L    \
        	+vcs+lic+wait     \
        	$(cm_dir)        \
        	$(cm)            \
        	$(cm_name)       \
			-f  ../../filelist.f    \
        	-debug_access+all \
        	+define+$(TEST)     \
        	-ntb_opts uvm-1.1 \
        	-l compile.log  

compile_clean:
	@rm  -rf   $(PRJ_HOME)/compile/$(TEST)
	@rm  -rf   $(PRJ_HOME)/vdCovLog/
	@rm  -rf   $(PRJ_HOME)/urgReport
	@rm  -rf   $(PRJ_HOME)/novas.*
	@rm  -rf   $(PRJ_HOME)/vdb/$(TEST)


comp:compile_clean
	@if [ ! -d $(PRJ_HOME)/compile/$(TEST) ];   \
		then  mkdir -p $(PRJ_HOME)/compile/$(TEST);    \
	fi
	@if [ ! -d $(PRJ_HOME)/signal_list/$(module_name) ];   \
		then  mkdir -p $(PRJ_HOME)/signal_list/$(module_name);    \
	fi
	@if [ ! -f $(PRJ_HOME)/signal_list/$(module_name)/$(TEST).rc ];   \
		then  mkdir -p $(PRJ_HOME)/signal_list/$(module_name)/$(TEST).rc ;    \
	fi
	cd  $(PRJ_HOME)/compile/$(TEST);   \
	cp  $(PRJ_HOME)/makefile  $(PRJ_HOME)/compile/$(TEST);   \
	$(compile_option)


run_option := ./simv    \
			  +fsdb+force   \
			  -reportstats   \
			  $(cm_dir)       \
        	  $(cm)             \
              $(cm_name)          \
			  -cm_nocasedef         \
			  +UVM_TESTNAME=$(TEST)     \
			  +ntb_random_seed=$(SEED)  \
			  -l simulation.log        \


run_clean:
	@rm  -rf   $(PRJ_HOME)/simulation/$(TEST)_$(SEED)/csrc
	@rm  -rf   $(PRJ_HOME)/simulation/$(TEST)_$(SEED)/simv.daidir
	@rm  -rf   $(PRJ_HOME)/simulation/$(TEST)_$(SEED)/simv
	@rm  -rf   $(PRJ_HOME)/simulation/$(TEST)_$(SEED)/*.log
	@rm  -rf   $(PRJ_HOME)/simulation/$(TEST)_$(SEED)/makefile
	@rm  -rf   $(PRJ_HOME)/simulation/$(TEST)_$(SEED)/*.fsdb*
	@rm  -rf   $(PRJ_HOME)/simulation/$(TEST)_$(SEED)/ucli.key

run:run_clean
	@if [ ! -d $(PRJ_HOME)/simulation/$(TEST)_$(SEED) ];   \
		then  mkdir -p $(PRJ_HOME)/simulation/$(TEST)_$(SEED);    \
	fi
	@cd  $(PRJ_HOME)/simulation/$(TEST)_$(SEED);         \
	cp  $(PRJ_HOME)/makefile  $(PRJ_HOME)/simulation/$(TEST)_$(SEED);   \
 	ln -s ${PRJ_HOME}/compile/$(TEST)/simv.daidir simv.daidir;    \
    ln -s ${PRJ_HOME}/compile/$(TEST)/csrc csrc;\
    ln -s ${PRJ_HOME}/compile/$(TEST)/simv simv;\
	$(run_option)     \
	# -gui=verdi


verdi:
	verdi -sv -uvm -f filelist.f -ssf $(PRJ_HOME)/simulation/$(TEST)_$(SEED)/tb.fsdb \
	-sswr  $(PRJ_HOME)/signal_list/$(module_name)/$(TEST).rc  -preTitle $(TEST)_$(SEED) -nologo &

cov_open:
	verdi  -cov -covdir $(PRJ_HOME)/vdb/simv_merge.vdb &

cov_merge:
	urg -full64 -dir $(merge_cov_file) -dbname ${PRJ_HOME}/vdb/simv_merge.vdb



# clean:
#     -rm -rf shared_record/ simv* test_* WORK/ *log urgReport/ vdCovLog/ rpt-auto/ merged_db/ comp_src/ novas.*

# run_ico:
#     ./simv -l sim.log +ntb_random_seed=${SEED} \
#     +ntb_solver_bias_mode_auto_config=2 \
#     +ntb_solver_bias_shared_record=${PRJ}/shared_record \
#     +ntb_solver_bias_wdir=ico_work \
#     +ntb_solver_bias_test_type=uvm \
#     +ntb_solver_bias_diag=3 
    

# crg_report:
#     crg -dir ${PRJ}/shared_record -report rpt-auto -format both -merge merged_db -zip 1 -illegal_group -illegal_attr



