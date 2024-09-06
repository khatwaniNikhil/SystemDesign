# Object storage
* user data stored as objects
* object = user data + metadata
* stored in flat structure
* hashing is used to generate object identifier/address
* read/write to complete objects only
* stores mix of structured and unstructured data

# Block storage
* user data is divided into blocks and each block stored separately.
* os gives each block has unique sequential address which is logged inside a lookup table
* read/write at block level
* NAS is just reading and writing blocks without managing the filesystem.
* client talking to NAS manages the filesystem
* fast read/writes
* AWS EBS used for databases and other transactional use cases storage needs 

# file storage 
* NAS is managing the filesystem, and dividing the files into blocks, and keeping track of which files are stored in which blocks.
* Computer accessing the NAS doesn't need to know what the filesystem is or how the files are stored,
it's just sending files to the NAS, requesting files from the NAS, and the NAS is keeping track of everything.
* In the cloud, file storage service may be set up over underlying physical block storage.
