pipeline {
    agent any

    stages {
        stage('Download from GitHub to Puppet') {
            steps {
                sh '/opt/puppetlabs/bin/puppet-code deploy production --wait'
            }
        }
        stage('Testing in Virtual Env') {
            steps {
                echo "Create chechpoint configuration"
                sh "/opt/puppetlabs/bin/puppet-task run yang_ietf::checkpoint target='csr1kv-local-1' --nodes 'cisco-pe-demo.localhost'"
                echo "Execute Puppet Device"
                sh "/opt/puppetlabs/bin/puppet-task run puppet_device target='csr1kv-local-1' --nodes 'cisco-pe-demo.localhost'"
                echo "Validate Changes"
                script {
                    try {
                        sh "/opt/puppetlabs/bin/puppet-task run yang_ietf::forbidden target='csr1kv-local-1' forbidden='apic' --nodes 'cisco-pe-demo.localhost'"
                    } catch (error) {
                        sh "/opt/puppetlabs/bin/puppet-task run yang_ietf::rollback target='csr1kv-local-1' --nodes 'cisco-pe-demo.localhost'"
                        error("Changes failed testing.  Rolled back.")
                    }
                }
                echo 'Copy run-start'
                sh "/opt/puppetlabs/bin/puppet-task run yang_ietf::saveconfig target='csr1kv-local-1' --nodes 'cisco-pe-demo.localhost'"
            }
        }
        stage('Physical Acceptance Env') {
            steps {
                echo "Create chechpoint configuration"
                sh "/opt/puppetlabs/bin/puppet-task run yang_ietf::checkpoint target='cat9k-staging' --nodes 'cisco-pe-demo.localhost'"
                echo "Execute Puppet Device"
                sh "/opt/puppetlabs/bin/puppet-task run puppet_device target='cat9k-staging' --nodes 'cisco-pe-demo.localhost'"
                echo "Validate Changes"
                script {
                    try {
                        sh "/opt/puppetlabs/bin/puppet-task run yang_ietf::forbidden target='cat9k-staging' forbidden='apic' --nodes 'cisco-pe-demo.localhost'"
                    } catch (error) {
                        sh "/opt/puppetlabs/bin/puppet-task run yang_ietf::rollback target='cat9k-staging' --nodes 'cisco-pe-demo.localhost'"
                        error("Changes failed testing.  Rolled back.")
                    }
                }
                echo 'Copy run-start'
                sh "/opt/puppetlabs/bin/puppet-task run yang_ietf::saveconfig target='cat9k-staging' --nodes 'cisco-pe-demo.localhost'"
            }
        }
        stage('Deploy and Validate in Production') {
            steps {
                echo "Create chechpoint configuration"
                sh "/opt/puppetlabs/bin/puppet-task run yang_ietf::checkpoint target='cat9k-production' --nodes 'cisco-pe-demo.localhost'"
                echo "Execute Puppet Device"
                sh "/opt/puppetlabs/bin/puppet-task run puppet_device target='cat9k-production' --nodes 'cisco-pe-demo.localhost'"
                echo "Validate Changes"
                script {
                    try {
                        sh "/opt/puppetlabs/bin/puppet-task run yang_ietf::forbidden target='cat9k-production' forbidden='apic' --nodes 'cisco-pe-demo.localhost'"
                    } catch (error) {
                        sh "/opt/puppetlabs/bin/puppet-task run yang_ietf::rollback target='cat9k-production' --nodes 'cisco-pe-demo.localhost'"
                        error("Changes failed testing.  Rolled back.")
                    }
                }
                echo 'Copy run-start'
                sh "/opt/puppetlabs/bin/puppet-task run yang_ietf::saveconfig target='cat9k-production' --nodes 'cisco-pe-demo.localhost'"
            }
        }
    }
}
