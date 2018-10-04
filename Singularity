bootstrap:docker
From:centos:6

%files 
NAMD_2.12_Linux-x86_64-multicore.tar.gz /opt

%post

yum -y upgrade
yum -y install epel-release yum-plugin-priorities

# osg repo
yum -y install http://repo.opensciencegrid.org/osg/3.4/osg-3.4-el6-release-latest.rpm

# pegasus repo
echo -e "# Pegasus\n[Pegasus]\nname=Pegasus\nbaseurl=http://download.pegasus.isi.edu/wms/download/rhel/6/\$basearch/\ngpgcheck=0\nenabled=1\npriority=50" >/etc/yum.repos.d/pegasus.repo

# well rounded basic system to support a wide range of user jobs
yum -y grouplist
yum -y groupinstall "Additional Development" \
                    "Compatibility Libraries" \
                    "Console Internet Tools" \
                    "Development Tools" \
                    "Internet Applications" \
                    "Networking Tools" \
                    "Scientific Support"

yum -y install \
    redhat-lsb \
    astropy-tools \
	bc \
	binutils \
	binutils-devel \
	coreutils \
	curl \
	fontconfig \
	gcc \
	gcc-c++ \
	gcc-gfortran \
	git \
	glew-devel \
	glib2-devel \
	glib-devel \
	graphviz \
	gsl-devel \
	java-1.8.0-openjdk \
	java-1.8.0-openjdk-devel \
	libgfortran \
	libGLU \
	libX11-devel \
	libXaw-devel \
	libXext-devel \
	libXft-devel \
	libxml2 \
	libxml2-devel \
	libXmu-devel \
	libXpm \
	libXpm-devel \
	libXt \
	mesa-libGL-devel \
	numpy \
	octave \
	octave-devel \
	osg-wn-client \
	openssl098e \
	p7zip p7zip-plugins \
	python-astropy \
	python-devel \
	R-devel \
	redhat-lsb-core \
	rsync \
	scipy \
	subversion \
	tcl-devel \
	tcsh \
	time \
	tk-devel \
	wget \
	which

# Cuda and cudnn - in case we land on GPU nodes. See:
#  https://developer.nvidia.com/cuda-downloads
#  https://gitlab.com/nvidia/cuda/blob/centos7/9.0/devel/cudnn7/Dockerfile
rpm -Uvh https://developer.download.nvidia.com/compute/cuda/repos/rhel6/x86_64/cuda-repo-rhel6-9.0.176-1.x86_64.rpm \
    && yum -y clean all \
    && yum -y install cuda-9-1 \
    && cd /usr/local \
    && curl -fsSL http://developer.download.nvidia.com/compute/redist/cudnn/v7.0.5/cudnn-9.1-linux-x64-v7.tgz -O \
    && tar --no-same-owner -xzf cudnn-9.1-linux-x64-v7.tgz -C /usr/local \
    && rm -f cudnn-9.1-linux-x64-v7.tgz \
    && ldconfig

# osg
# use CA certs from CVMFS
yum -y install osg-ca-certs osg-wn-client
mv /etc/grid-security/certificates /etc/grid-security/certificates.osg-ca-certs
ln -f -s /cvmfs/oasis.opensciencegrid.org/mis/certificates /etc/grid-security/certificates

# htcondor - include so we can chirp
yum -y install condor

# pegasus
yum -y install pegasus

# required directories
for MNTPOINT in \
    /cvmfs \
    /hadoop \
    /hdfs \
    /lizard \
    /mnt/hadoop \
    /mnt/hdfs \
    /xenon \
    /spt \
    /stash2 \
; do \
    mkdir -p $MNTPOINT ; \
done

# make sure we have a way to bind host provided libraries
# see https://github.com/singularityware/singularity/issues/611
mkdir -p /host-libs /etc/OpenCL/vendors

# build info
echo "Timestamp:" `date --utc` | tee /image-build-info.txt

cd /opt
tar -xzvf NAMD_2.12_Linux-x86_64-multicore.tar.gz

echo 'export PATH=/opt/NAMD_2.12_Linux-x86_64-multicore:$PATH' >>$SINGULARITY_ENVIRONMENT

% environment
export PATH=/opt/NAMD_2.12_Linux-x86_64-multicore:$PATH
