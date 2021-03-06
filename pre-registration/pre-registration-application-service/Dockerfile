FROM openjdk:11

# can be passed during Docker build as build time environment for github branch to pickup configuration from.
ARG spring_config_label

# can be passed during Docker build as build time environment for spring profiles active 
ARG active_profile

# can be passed during Docker build as build time environment for config server URL 
ARG spring_config_url

# can be passed during Docker build as build time environment for glowroot 
ARG is_glowroot

# can be passed during Docker build as build time environment for artifactory URL
ARG artifactory_url

# environment variable to pass active profile such as DEV, QA etc at docker runtime
ENV active_profile_env=${active_profile}

# environment variable to pass github branch to pickup configuration from, at docker runtime
ENV spring_config_label_env=${spring_config_label}

# environment variable to pass spring configuration url, at docker runtime
ENV spring_config_url_env=${spring_config_url}

# environment variable to pass glowroot, at docker runtime
ENV is_glowroot_env=${is_glowroot}

# environment variable to pass artifactory url, at docker runtime
ENV artifactory_url_env=${artifactory_url}

copy ./target/pre-registration-application-*.jar pre-registration-application-service.jar

EXPOSE 9090
CMD if [ "$is_glowroot_env" = "present" ]; then \
    wget "${artifactory_url_env}"/artifactory/libs-snapshot-local/io/mosip/kernel/kernel-ref-idobjectvalidator/kernel-ref-idobjectvalidator.jar kernel-ref-idobjectvalidator.jar ; \
    wget "${artifactory_url_env}"/artifactory/libs-release-local/io/mosip/testing/glowroot.zip ; \
    apt-get update && apt-get install -y unzip ; \
    unzip glowroot.zip ; \
    rm -rf glowroot.zip ; \
    
    sed -i 's/<service_name>/pre-registration-application-service/g' glowroot/glowroot.properties ; \
    java -Dloader.path=/kernel-ref-idobjectvalidator.jar -jar -javaagent:glowroot/glowroot.jar -Dspring.cloud.config.label="${spring_config_label_env}" -Dspring.profiles.active="${active_profile_env}" -Dspring.cloud.config.uri="${spring_config_url_env}" pre-registration-application-service.jar ; \
    else \
    wget "${artifactory_url_env}"/artifactory/libs-snapshot-local/io/mosip/kernel/kernel-ref-idobjectvalidator/kernel-ref-idobjectvalidator.jar kernel-ref-idobjectvalidator.jar ; \
    java -Dloader.path=/kernel-ref-idobjectvalidator.jar -jar -Dspring.cloud.config.label="${spring_config_label_env}" -Dspring.profiles.active="${active_profile_env}" -Dspring.cloud.config.uri="${spring_config_url_env}" pre-registration-application-service.jar ; \
    fi

#CMD ["java","-Dloader.path=/kernel-ref-idobjectvalidator-0.9.1.jar","-jar","-Dspring.cloud.config.label=${spring_config_label_env}","-Dspring.profiles.active=${active_profile_env}","-Dspring.cloud.config.uri=${spring_config_url_env}","pre-registration-application-service.jar"]
