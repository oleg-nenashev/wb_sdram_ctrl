import org.librecores.ci.LCCI;
import org.librecores.fusesoc.FuseSoCBuilder;

def commitId = null;
def organization = 'oleg-nenashev'
def coreName = 'wb_sdram_ctrl'

stage('Determine required patches') {
  node("librecores-ci") {
    // Now we take only one repo, but maybe we need more ones later
    new LCCI(this).checkoutScmOrFallback("https://github.com/${organization}/${coreName}.git")
    sh 'git rev-parse HEAD > GIT_COMMIT'
    commitId = readFile('GIT_COMMIT')
  }
}

stage('Build component') {
  node('docker-fusesoc-icarus') {
    FuseSoCBuilder.sim(this, coreName, organization, commitId,
      "/fusesoc", "--transactions 10")
  }
}
