# Before building this image oracle/database:12.1.0.2-ee must be available at your local registry
# See https://github.com/oracle/docker-images/blob/master/OracleDatabase/README.md for more details
# Finally build a new image using ./buildDockerImage.sh script
# To run a container using patch installed image you should pass at least three parameters:
#    ORACLE_SID
#    ORACLE_PDB
# for example:
# docker run -d --privileged=true --name test --hostname test \
#   -p 1521:1521 -p 5500:5500 \
#   -e ORACLE_SID=TEST \
#   -e ORACLE_PDB=PDB1 \
#   -v /home/data/db/test:/opt/oracle/oradata \
#   oracle/database:12.2.0.1.170718-ee

FROM	oracle/database/12.2.0.1:base
MAINTAINER pete@dinin.net    

ENV  OJVM_PATCH="28440725" \
     RU_PATCH="28662603" \ 
     OPATCH_FILE="p6880880_180000_Linux-x86-64.zip"

ENV  OJVM_PATCH_FILE="p"$OJVM_PATCH"_122010_Linux-x86-64.zip" \
     RU_PATCH_FILE="p"$RU_PATCH"_122010_Linux-x86-64.zip"

USER    root
COPY    --chown=oracle:dba $RU_PATCH_FILE $OJVM_PATCH_FILE $OPATCH_FILE $INSTALL_DIR/

USER    oracle
RUN	unzip -qo $INSTALL_DIR/$OPATCH_FILE -d $ORACLE_HOME && \
        unzip -qo $INSTALL_DIR/$RU_PATCH_FILE -d $INSTALL_DIR && \
        unzip -qo $INSTALL_DIR/$OJVM_PATCH_FILE -d $INSTALL_DIR && \
	$ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -ph $INSTALL_DIR/$RU_PATCH && \
	$ORACLE_HOME/OPatch/opatch apply -silent $INSTALL_DIR/$RU_PATCH && \
        $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -ph $INSTALL_DIR/$OJVM_PATCH && \
        $ORACLE_HOME/OPatch/opatch apply -silent $INSTALL_DIR/$OJVM_PATCH  && \
        rm -rf $ORACLE_HOME/.patch_storage && \
        rm $ORACLE_HOME/bin/*O && \
        rm $ORACLE_HOME/bin/*0 && \
        rm -rf $INSTALL_DIR/*

USER    root
RUN	rm -rf $INSTALL_DIR

USER oracle
WORKDIR /home/oracle


