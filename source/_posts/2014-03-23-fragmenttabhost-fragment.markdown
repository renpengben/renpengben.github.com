---
layout: post
title: "FragmentTabHost切换Fragment时避免重复加载UI"
date: 2014-03-23 15:38:15 +0800
comments: true
categories: android
---
<span>使用FragmentTabHost时，Fragment之间切换时每次都会调用onCreateView方法，导致每次Fragment的布局都重绘，无法保持Fragment原有状态。</span>

<span>解决办法：在Fragment onCreateView方法中缓存View。<span>
        //缓存Fragment view
      	private View rootView;
	
	    @Override
	    public View onCreateView(LayoutInflater inflater, ViewGroup container, 
	    Bundle     savedInstanceState) {
		if(rootView==null){
			rootView=inflater.inflate(R.layout.tab_fragment, null);
		}
        //缓存的rootView需要判断是否已经被加过parent， 如果有parent需要从parent删除，
        //要不然会发生这个rootview已经有parent的错误。    
        ViewGroup parent = (ViewGroup) rootView.getParent();
        if (parent != null) {
            parent.removeView(rootView);
        } 
		return rootView;
       	}