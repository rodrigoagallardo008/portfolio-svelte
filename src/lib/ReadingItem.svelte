<script>
  export let data = {};
</script>

<div>
  <img src={data.image} alt={data.title} />
  <strong>{data.title}</strong>
  <span>{data.author}</span>
</div>
