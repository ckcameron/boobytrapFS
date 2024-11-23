# A FUSE-backend-rs-based passthrough filesystem to execute code on FUSE ops!

The code has yet to be posted with my modifictions, and this is a placeholder for when I publish the first tersting versions. Keep your eyes peeled for more.

Original project:
![Crates.io](https://img.shields.io/crates/l/fuse-backend-rs)
[![Crates.io](https://img.shields.io/crates/v/fuse-backend-rs)](https://crates.io/crates/fuse-backend-rs)

## Oh dear non-existent gods, what have you done?!?

So I added fuctioality to the fuse-backed-rs passthrough example filesystem to execute configurable arbitrary code before or after specific FUSE operations on the mounted directory or specific structures within.

## That's uholy and obscene! Why why why?

Why?? Why not, I ask? Sure this kinnd of super power should be wielded carefully and you can totally bork your stuff, but that os true with just about anything whe it comes to filesystems or arbitrary execution of any kind. 


Active file monitoring takes up system resources and has a more limited scope in terms of actions one can take in respose to activity on monitored files. Usinng the FUSE-layer filesystem perations as the triggers themselves, one can passively effect protocols and pull tricks one could not with active supra-filesystem checks. 


## Alright, salesman, how does this snake-oil work, exactly?
The key is passive structural-operation-initiated execution. Whenn something attempts to read, write or stat particular structures within the mount, the filesystem operation itself will execute configured code before or after the interaction with the structure.

## So what is this FS actually doing?

Without an active event-driven process or daemon, the directory mounted is locked an overlay mount is placed on top of it. This overlay passes through all operations to the underlying filesystem, but it does so after executing the configured pre-operation code, and returns operations to the requestor after receiving a return from the underlying filesystem and conditionally executing the configured post-operation code. The execution configurable assumes a bash-like envoronmet and syntax, although this will probably be changeable in future releases.

## So why should I care?

You shouldn't. Or you should if you want to have reliable conditional execution that is baked-into the filesystem layer and all interactios with the data concerned.

You can do some really ugly, sloppy, terrible, rash, career-ending stuff that may or may not be super useful with this tool. Or you can have a lot of fun screwing with folks' minds, if you're something of an imp, like myself.

## Like screwing with their minds HOW, exactly?

Well funny you should ask! Here are some examples:

Scenario 1: You have a bunch of config files you want all versions of kept and a copy of the old version made before any write to files in the mounted directory.

When your fabulous admiistrator updates the files, the write call is split: before the write is performed, a copy of the existing file is made to another directory on the system and the name augemented to indicate the time, date and changing user of the file concerned. Once the copy is sucessfully written and only then, the overwriting of the original file is performed, and a sucessful write is returned to the user.

Scenario 2: Your fabulous administrator is kind of a schmuck and you want to give him an ulcer.

After the sucessful write of the new version of the file is completed, it the write-requesting user is checked, and if it is your target schmuck, then a rename is made of the proper config file to something similar and hidden, and the connfig file under the name requested is filled with garbage and  mucked-up on every new read with somethinng bizzare for the the next x minutes. X miutes is the time it takes for your admiistrator to be worried, totally-confused, frustrated, and not long ennough for the mucking about to be apparent to anyone they try to tell about the nonsense they are witnessing. The closure of the timed bash script includes a moving of the hiddenn proper data to the appripriate filename, so your admin not only goes crazy, but looks crazy to anyonne he attempts to explain it to. It is a fast-track to an ulcer and a personal bouncey-castle (padded room) in ann institution far from sharp edges or blunt surfaces for your admin and fu for the whole fam-damnly.

Lesss sadistically I have found these kind of conditional executions helpful in my CI/CD/Code Pipelines, cloud devOps/admin, infosec management, and general systems tomfoolery in a number of ways. 

Yes, we drink our own kool-aide and everything. If y'all end-up like those in Jonestown, I disclaim any responsibility. I told you this was all unholy. 

I will be including the configs that would do such a thing  shortly, but for now a description will have to do.

##Below from original readme - WIP status and I onnly have so many moments of distraction to spare.
![arch](docs/images/fuse-backend-architecture.svg)

## Examples

### Filesystem Drivers
- [Virtual File System](https://github.com/cloud-hypervisor/fuse-backend-rs/tree/master/src/api/vfs)
  for an example of union file system.
- [Pseudo File System](https://github.com/cloud-hypervisor/fuse-backend-rs/blob/master/src/api/pseudo_fs.rs)
  for an example of pseudo file system.
- [Passthrough File System](https://github.com/cloud-hypervisor/fuse-backend-rs/tree/master/src/passthrough)
  for an example of passthrough(stacked) file system.

  
## License
This project is licensed under
- [Apache License](http://www.apache.org/licenses/LICENSE-2.0), Version 2.0
- [BSD-3-Clause License](https://opensource.org/licenses/BSD-3-Clause)
