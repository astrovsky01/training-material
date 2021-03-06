# Galaxy - Using Galaxy tools to generate new Galaxy tools
#
# To build the docker image, go to root of the training repo and
#    docker build -t tool-generators -f topics/tool-generators/docker/Dockerfile .
# Take a break. Takes a while!
# To run image, make a `planemo --extra_tools` tool directory where you want to run it regularly and then
#    docker run -p "9090:9090" -v mytools:/planemo/mytools/  -t tool-generators
# ToolFactory planemo will be available on localhost:9090
# Toolshed archives you generate can be downloaded and then unpacked under the mytools directory.
# They will be loaded by planemo into the Galaxy it runs and be available in the tool menu the next time you restart the container and planemo
# This allows you to load newly generated tools for testing and refinement.
# WARNING: Export your history before you shut this container down or lose your ToolFactory form work when you stop the container.
# Planemo always starts with an empty history.
#
# Rate limited on ubuntu image so using a quay.io minimal
# derived from https://github.com/cybozu/ubuntu-base/tree/main/20.04/ubuntu
#    docker run -d --privileged -p 8080:80 -p 9090:9090  -v /var/run/docker.sock:/var/run/docker.sock \
#       -v /home/ross/rossgit/planemo/mytools:/planemo/mytools \
#       quay.io/fubar2/toolfactory_tutorial:latest
# So, please make your own script like shown here and save as start.sh
# the volume can be populated with newly generated tools from unpacked toolshed archives and remains persistent
# history WILL NOT persist so save it if you want to import it in the future to save typing all your work again.

# biocontainer should involve a little less waiting at the first run - and first impressions count.

FROM quay.io/cybozu/ubuntu-minimal:focal-20210217
ENV DEBIAN_FRONTEND=noninteractive
MAINTAINER Ross Lazarus
# with fixes by Helena Rasch
ENV GALDIR "/galaxy-central"
ENV PDIR "/planemo"
RUN apt-get update \
&& apt-get -y upgrade \
&& apt-get install -y -qq --no-install-recommends locales tzdata openssl netbase apt-utils apt-transport-https libreadline8  \
 software-properties-common ca-certificates curl python3-dev gcc python3-pip build-essential python3-venv \
 python3-wheel nano wget git python3-setuptools gnupg mercurial \
&& curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
&& locale-gen en_US.UTF-8 \
&& update-locale LANG=en_US.UTF-8 \
&& dpkg-reconfigure -f noninteractive tzdata \
&& curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add - \
&& add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
&& apt-get -y update  \
&& apt install -y docker-ce-cli \
&& python3 -m pip install --upgrade pip \
&& mkdir -p $GALDIR \
&& curl -L -s https://github.com/galaxyproject/galaxy/archive/dev.tar.gz | tar xzf - --strip-components=1 -C $GALDIR \
&& git clone --recursive https://github.com/fubar2/planemo.git $PDIR \
&& cp $PDIR/planemo_ext/welcome.html $GALDIR/static/welcome.html.sample \
&& cp $PDIR/planemo_ext/welcome.html $GALDIR/static/welcome.html \
&& mkdir $PDIR/mytools \
&& rm -rf $PDIR/doc \
&& cd $PDIR \
&& python3 setup.py build \
&& python3 setup.py install \
&& pip install -U pip \
&& planemo conda_init --conda_prefix $PDIR/con \
&& hg clone https://fubar@toolshed.g2.bx.psu.edu/repos/fubar/tacrev $PDIR/tacrev \
&& cp $GALDIR/config/datatypes_conf.xml.sample $GALDIR/config/datatypes_conf.xml \
&& sed -i 's/<\/registration>/<datatype extension="tgz" type="galaxy.datatypes.binary:Binary" subclass="true" mimetype="multipart\/x-gzip" display_in_upload="true"\/> <\/registration>/' $GALDIR/config/datatypes_conf.xml \
&& sed -i 's/<datatype extension="html"/<datatype extension="html" display_in_upload="true"/' $GALDIR/config/datatypes_conf.xml \
&& sed -i 's/<datatype extension="toolshed.gz"/<datatype extension="toolshed.gz" display_in_upload="true" /' $GALDIR/config/datatypes_conf.xml \
&& sed -i '/-l|-list|--list)/i \\n\t --dev-wheels|-dev-wheels)\n\t\tshift\n\t\t;;\n' $GALDIR/run_tests.sh \
&& virtualenv /root/.planemo/gx_venv_3.9 \
&& . /root/.planemo/gx_venv_3.9/bin/activate && pip install -U setuptools \
&& cd $GALDIR && export GALAXY_VIRTUAL_ENV=/root/.planemo/gx_venv_3.9 && make setup-venv \
&& planemo test --galaxy_root $GALDIR $PDIR/tacrev/tacrev/tacrev.xml \
&& rm -rf /usr/local/share/.cache/yarn \
&& apt-get clean && apt-get purge \
&& rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
VOLUME /planemo/mytools
WORKDIR $PDIR
ENV GALAXY_CONFIG_BRAND "ToolFactory in Planemo"
EXPOSE 9090
ENTRYPOINT ["/usr/local/bin/planemo" ,"tool_factory", "--galaxy_python_version", "3.9", "--galaxy_root" ,"/galaxy-central", "--port", "9090", "--host", "0.0.0.0", "--conda_prefix", "/planemo/con", "--extra_tools", "/planemo/mytools", "--galaxy_python_version", "3.9"]

