#zbs

```python
def calc_volume_offset(vextent_offset, vextent_no, stripe_num=8) -> int:
    stripe_size = 256 << 10
    extent_size = 256 << 20
    stripe_group_size = extent_size * stripe_num

    stripe_group = vextent_no // stripe_num 
    stripe_group_idx = vextent_no % stripe_num
    stripe_idx = vextent_offset // stripe_size
    stripe_offset = vextent_offset % stripe_size

    volume_offset = (stripe_group * stripe_group_size) + (stripe_idx * stripe_size * stripe_num) + stripe_group_idx * stripe_size + stripe_offset
    return volume_offset

def calc_vextent_id_and_extent_offset(volume_offset, stripe_num=8) -> typing.Tuple[int, int]:
    extent_size = 256 << 20
    stripe_group_size = extent_size * stripe_num

    stripe_group_idx = volume_offset // stripe_group_size
    stripe_group_offset = ((volume_offset % stripe_group_size) // (256 << 10)) // stripe_num
    stripe_idx = ((volume_offset % stripe_group_size) // (256 << 10)) % stripe_num

    base_vextent_offset = stripe_group_offset * 256 * 1024

    vextent_no = stripe_group_idx * stripe_num + stripe_idx
    offset_in_vextent = (volume_offset % (256<<10)) + base_vextent_offset

    return vextent_no, offset_in_vextent
```
