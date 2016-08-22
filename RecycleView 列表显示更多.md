	 <TextView
	        android:id="@+id/tv_share_item_content"
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        android:ellipsize="end"
	        android:maxLines="3"
	        android:textSize="16sp"
	        android:padding="16dp"
	        tools:text="Autumn mountain cheshen!!"/>



Adapter实现

	public class DiscoverAdapter extends RecyclerView.Adapter<DiscoverAdapter.ItemView> {
	
	    private SparseBooleanArray expandStates = new SparseBooleanArray();
	    private List<ShareInfo> data;
	    private RvItemClickListener itemClickListener;
	
	
	    public DiscoverAdapter(RvItemClickListener itemClickListener) {
	        this.data = new ArrayList<>();
	        this.itemClickListener = itemClickListener;
	    }
	
	    public void addHead(List<ShareInfo> newData) {
	        data.addAll(0, newData);
	        notifyItemRangeInserted(0, newData.size());
	    }
	
	    public void addFooter(List<ShareInfo> newData) {
	        data.addAll(newData);
	        int size = data.size() - 1;
	        notifyItemInserted(size);
	    }
	
	    public void setData(List<ShareInfo> data) {
	        this.data = data;
	        notifyDataSetChanged();
	    }
	
	    public List<ShareInfo> getData() {
	        return data;
	    }
	
	    @Override
	    public ItemView onCreateViewHolder(ViewGroup parent, int viewType) {
	        this.context = parent.getContext();
	        View view = LayoutInflater.from(parent.getContext()).inflate(R.layout.share_discover_item, parent, false);
	        return new ItemView(view);
	    }
	
	    @Override
	    public void onBindViewHolder(ItemView holder, int position) {
	        ShareInfo item = data.get(position);   
	        holder.contentTv.setText(item.getTitle());
	        holder.contentTv.setTag(position);
	        holder.contentTv.setOnClickListener(new View.OnClickListener() {
	            @Override
	            public void onClick(View v) {
	                TextView expandTv = (TextView) v;
	                int pos = (int) expandTv.getTag();
	                expandTv.setMaxLines(100);
	                expandStates.put(pos, true);
	            }
	        });
	        if (expandStates.get(position)) {
	            holder.contentTv.setMaxLines(100);
	        }
	    }
	
	    @Override
	    public int getItemCount() {
	        return data.size();
	    }
	
	    public class ItemView extends RecyclerView.ViewHolder implements View.OnClickListener {
	
	        private TextView contentTv;
	
	
	        public ItemView(View itemView) {
	            super(itemView);
	            contentTv = (TextView) itemView.findViewById(R.id.tv_share_item_content);
	            itemView.setOnClickListener(this);
	        }
	
	        @Override
	        public void onClick(View v) {
	            itemClickListener.onItemClick(v, this.getLayoutPosition());
	        }
	    }
	}