git clone https://github.com/citron/dataiku-tools.git
cd dataiku-tools/dss-docker
export DSS_VERSION=9.0.0
docker build -t chu/dss:"$DSS_VERSION" --build-arg dssVersion="$DSS_VERSION" .
#docker run -p 10000:10000 -v /home/gacquewi/DATAIKU/dss:/home/dataiku/dss -d dataiku/dss
docker run -p 10000:10000 -v /home/gacquewi/DATAIKU/dss:/home/dataiku/dss -d chu/dss:"$DSS_VERSION"
