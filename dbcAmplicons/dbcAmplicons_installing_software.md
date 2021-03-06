# Installing the workshop software

We are going to install the software needed for this workshop

1. FLASH2 to overlap reads (https://github.com/dstreett/FLASH2)
2. RDP to classify reads into taxon (https://github.com/rdpstaff/RDPTools)
3. dbcAmplicons pipeline for processing amplicon sequences to abundance tables (https://github.com/msettles/dbcAmplicons)

optional

4. bowtie2 (https://github.com/BenLangmead/bowtie2)

**System Requirements**

* git
* java jdk
* ant
* python virtualenv
* biom requires numpy

---

**1\.** First, create a directory for the workshop:

    cd
    mkdir -p /share/workshop/$USER/mca_example

and two other directors

	mkdir /share/workshop/$USER/mca_example/src
	mkdir /share/workshop/$USER/mca_example/bin
	
---
---
	
**2\.** Now lets add the new bin directory to our PATH in a dbcA_profile file

	echo 'export PATH=/share/workshop/$USER/mca_example/bin:$PATH' > /share/workshop/$USER/mca_example/src/dbcA_profile

Then on the command line, execute the commands in the dbcA_profile file using source.

	source /share/workshop/$USER/mca_example/src/dbcA_profile

---

**3\.** Install **FLASH2** into src and link the executable into bin

	cd /share/workshop/$USER/mca_example/src
	git clone https://github.com/dstreett/FLASH2.git
	cd FLASH2
	make
	ln -s /share/workshop/$USER/mca_example/src/FLASH2/flash2 /share/workshop/$USER/mca_example/bin/.
	# test installation, should see help documentation
	flash2 -h

---

**4\.a** Install apache ant, need for RDP

	cd /share/workshop/$USER/mca_example/src
	curl http://mirrors.ibiblio.org/apache/ant/binaries/apache-ant-1.10.5-bin.tar.gz > apache-ant-1.10.5-bin.tar.gz
	tar xzvf apache-ant-1.10.5-bin.tar.gz
	ln -s /share/workshop/$USER/mca_example/src/apache-ant-1.10.5/bin/ant /share/workshop/$USER/mca_example/bin/.

**4\.b** Install the **Ribosomal Database Project** (RDP) into src

	module load java/jdk1.8 # Need the java jdk1.8 loaded

	cd /share/workshop/$USER/mca_example/src
	git clone https://github.com/rdpstaff/RDPTools.git
	cd RDPTools/
	git submodule init
	git submodule update
	make

might have problems retrieving data.tgz file ... if so, try again ... during 9-5 EST(!) ...  

test installation, should see help documentation for classify

	java -jar classifier.jar classify

this should give you a "Command Error" because you didn\'t specify output, but it should give you a list of options.
feel free to move on if the 'make' fails due to data.tgz file, we can fix this later

**4\.c** Add the location of classifier.jar as a variable in our dbcA_profile file,


	echo 'module load java/jdk1.8' >> /share/workshop/$USER/mca_example/src/dbcA_profile
	echo 'export RDP_PATH=/share/workshop/$USER/mca_example/src/RDPTools' >> /share/workshop/$USER/mca_example/src/dbcA_profile

Then on the command line, execute the commands in the dbcA_profile file using source.

	source /share/workshop/$USER/mca_example/src/dbcA_profile

---

**5\.a** Setup a conda python environment for dbcAmplicons, in the src directory.

	module load anaconda2
	conda create -p /share/workshop/$USER/mca_example/src/dbcA_virtualenv python=2.7 pip

**5\.b** This lets you set the virtual environment to activate on login by adding it to our dbcA_profile file.

	echo 'module load anaconda2' >> /share/workshop/$USER/mca_example/src/dbcA_profile
	echo '. /software/anaconda2/4.5.12/lssc0-linux/etc/profile.d/conda.sh' >>  /share/workshop/$USER/mca_example/src/dbcA_profile
	echo 'conda activate /share/workshop/$USER/mca_example/src/dbcA_virtualenv' >> /share/workshop/$USER/mca_example/src/dbcA_profile
	echo 'export PYTHON_EGG_CACHE=/share/workshop/$USER/mca_example/src' >> /share/workshop/$USER/mca_example/src/dbcA_profile


Then on the command line, execute the commands in the dbcA_profile file using source.

	source /share/workshop/$USER/mca_example/src/dbcA_profile

You should now see the text "(dbcA_virtualenv)" at the beginning of your prompt.
---

**6\.** Install **dbcAmplicons**

	cd /share/workshop/$USER/mca_example/src
	easy_install pip
	pip install biom_format
	git clone https://github.com/msettles/dbcAmplicons.git
	cd /share/workshop/$USER/mca_example/src/dbcAmplicons/
	python setup.py install

test installation, should see help documentation

	dbcAmplicons -h

You should see the options / usage message

**Optional\.** Test **dbcAmplicons**

You could also test the dbcAmplicons installation by running the script, test_dbAmplicons.sh, under the tests folder (in dbcAmplicions).

	cd /share/workshop/$USER/mca_example/src/dbcAmplicons/tests/
	./test_dbAmplicons.sh

The script should show some ERRORs / WARNINGs (intended as test), but otherwise give stats after a few minutes

---

**Lets Review**

We created a directory for the workshop, in that directory we created two folders src and bin. We've installed FLASH2, RDP and dbcAmplicons. We've placed the executable for flash in a bin folder and added the folder to our PATH. We created an environment variable for the RDP classifier. We've created a conda python environment and then installed the dbcAmplicons package using setup.py.

1. You should have verified all the software works by viewing their help docs
2. Verify that the flash executable is indeed in the bin folder
3. We've modified your PATH and added 1 new environment variable, RDP_PATH, verify the PATH and the new env variable.
4. We added a multiple lines to your \.bash_profile. How many lines?

Now log out, log back in and verify that each application still works. Ex.

	flash2 -h

To verify RDP and dbcAmplicons use

	java -jar $RDP_PATH/classifier.jar classify
	dbcAmplicons -h

You can check the current version of everything with

	dbcVersionReport.sh

We usually save the output of this in the project file to remind ourselves which versions of software were run for that project.

If for some reason installation failed let me know and we'll get things fixed

These lines should be in your dbcA_profile

<div class="script">export PATH=/share/workshop/$USER/mca_example/bin:$PATH
module load java/jdk1.8
export RDP_PATH=/share/workshop/$USER/mca_example/src/RDPTools
module load anaconda2
. /software/anaconda2/4.5.12/lssc0-linux/etc/profile.d/conda.sh
conda activate /share/workshop/$USER/mca_example/src/dbcA_virtualenv
</div>

At any time you can initiate the environment for dbcAmplicons using.

    source /share/workshop/$USER/mca_example/src/dbcA_profile

and you can specifically deactivate the conda environment with

    conda deactivate
