Running HAPSEG with the example data
------
The mix250K_seg_out.txt file should be used as the seg.file.
The birdclusters.RData file should be used as the clusters.file.
Each of the individual RData files should be passed as the snp.file in a 
separate HAPSEG run.

Parameter settings:
  plate.name = "DRAWS"
  array.name = <use the base name of each RData file>
  genome.build = "hg18"
  platform = "SNP_250K_STY"
  use.pop = "CEPH"
  impute.gt = FALSE
  plot.segfit = TRUE
  merge.small = TRUE
  merge.close = TRUE
  min.seg.size = 5
  normal = FALSE
  out.p = 0.001
  seg.merge.thresh = 1e-10
  use.normal = TRUE
  drop.x = TRUE
  drop.y = FALSE
  calibrate.data = TRUE (On)

Other relevant per-sample parameter info can be found in mix_250K_SIF.txt.
------
Running ABSOLUTE
------
For the seg.dat.file, use the RData output file from a previous HAPSEG run.
  sigma.p = 0
  max.sigma.h = 0.02
  min.ploidy = 0.95
  max.ploidy = 10
  max.as.seg.count = 1500
  max.non.clonal = 0
  max.neg.genome = 0
  copy.number.type = "allelic"
  platform = "SNP_250K_STY"

Other relevant per-sample parameter info can be found in mix_250K_SIF.txt.

------
Running ABSOLUTE.summarize
------
For the absolute.files.list, use a File List file containing paths to 
output RData files from previous ABSOLUTE (per-sample) runs.

  collection.name = "DRAWS_summary"
  copy.number.type = "allelic"
  plot.modes = TRUE

------
Running ABSOLUTE.review
------
For the reviewed.pp.calls.file, use the "calls" output file from
ABSOLUTE.summarize, but add a new column to the left of the existing
column.  The contents of each row in this column should be the
solution you have chosen after reviewing the results.  For testing
purposes, you can just enter some reasonable low-numbered value
like 1 or 2.

  analyst.id = <your user id>
  modes.file = <the "modes" file from ABSOLUTE.summarize>
  collection.name = "DRAWS_summary"
  copy.number.type = "allelic"
