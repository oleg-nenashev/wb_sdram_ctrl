def commitId = null;
def coreName = 'wb_sdram_ctrl';

stage 'Determine required patched'
node("librecores-ci") {
    // Now we take only one repo, but maybe we need more ones later
    checkout scm
    sh 'git rev-parse HEAD > GIT_COMMIT'
    commitId = readFile('GIT_COMMIT')
}

node('docker-fusesoc-icarus') {
    stage "Patch orpsoc-cores registry"
    sh "cd /fusesoc && rm -rf orpsoc-cores"
    dir('orpsoc-cores') {
        git 'https://github.com/openrisc/orpsoc-cores.git'
    }
    sh "cp -R orpsoc-cores /fusesoc/"
    // TODO: get rid of the name hardcode
    sh "ls -la /fusesoc/orpsoc-cores/cores/${coreName}/"
    
    patchCoreSource(coreName,'oleg-nenashev',commitId)

    stage "FuseSoC sim"
    try {
        sh 'fusesoc sim wb_sdram_ctrl transactions=10'
    } finally {
        archiveArtifacts artifacts: 'fusesoc.log', excludes: null
    }
}

void patchCoreSource(String _coreName, String _userName, String _version) {
    String filePath = "/fusesoc/orpsoc-cores/cores/${_coreName}/${_coreName}.core"
    String modified = "\n\n[provider] \nname = github \n" + 
        "user = ${_userName} \n" +
        "repo = ${_coreName} \n" +
        "version = ${_version}"
    sh "ls ${filePath}"
    String oldCoreDef = readFile(filePath)
    String newCoreDef = oldCoreDef.substring(0,oldCoreDef.indexOf('[provider]')) + modified;
    writeFile file: filePath, text: newCoreDef
}
